# Configuration Automation with Ansible

## What I was doing

I had already installed node_exporter by hand on three Proxmox nodes. I used Ansible to repeat that setup and then added a playbook for the Docker host. The original steps are in the [monitoring notes](../monitoring/monitoring-stack.md).

## Why I automated it

I was opening an SSH session to each node and repeating the same user, download, and systemd steps. Once I had it working manually, I wanted one playbook I could run across all three.

## Control Node

The existing Ubuntu Docker host (`10.10.10.103`) serves as the Ansible control node — it's already on the lab network with working SSH, and Ansible itself is agentless (no persistent service to run), so nothing new needed to be deployed for this.

```bash
sudo apt update
sudo apt install -y ansible
```

## Inventory

[`inventory/hosts.ini`](inventory/hosts.ini):

```ini
[proxmox]
pve-01 ansible_host=10.10.10.11
pve-02 ansible_host=10.10.10.12
pve-03 ansible_host=10.10.10.13

[proxmox:vars]
ansible_user=root

[docker_hosts]
ubuntu-srv-01 ansible_connection=local
```

`ubuntu-srv-01` uses `ansible_connection=local` since it's the same machine the control node runs on — no SSH loopback needed, just local privilege escalation via `become`.

## SSH Trust to the Proxmox Nodes

The control node needs a key trusted by root on each Proxmox node:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -N ""
ssh-copy-id root@10.10.10.11
ssh-copy-id root@10.10.10.12
ssh-copy-id root@10.10.10.13
```

## Playbook: `node_exporter.yml`

[`playbooks/node_exporter.yml`](playbooks/node_exporter.yml) reproduces the manual install from the monitoring setup, but idempotently — re-running it is a no-op once `node_exporter` is already installed and running, thanks to the `stat` check before downloading/installing:

```bash
cd ansible
ansible-playbook playbooks/node_exporter.yml
```

Unlike the original manual process, the version is pinned (`1.8.2`) as a playbook variable rather than resolved dynamically at install time — intentional, so a re-run of this playbook always produces the same result instead of picking up whatever the latest GitHub release happens to be on a given day.

## Playbook: `docker_host.yml`

[`playbooks/docker_host.yml`](playbooks/docker_host.yml) reproduces the Docker install from [Docker Host Setup](../proxmox/docker-host.md) and then brings up the monitoring stack from the cloned repo:

```bash
cd ansible
ansible-playbook playbooks/docker_host.yml --ask-become-pass
```

(`--ask-become-pass` because this one runs locally against `ubuntu-srv-01` and needs a `sudo` password rather than SSH key auth.)

![Ansible docker_host playbook run](../screenshots/monitoring/ansible-docker-host-run.png)

`ok=8, changed=2, failed=0`. The two `changed` tasks are worth explaining rather than glossing over:

- **Add Docker apt repository** — Ansible's `apt_repository` module manages that file in its own normalized format, which differed slightly from the file created by hand back in [Docker Host Setup](../proxmox/docker-host.md), so it rewrote it. A second run of just this task would show no change.
- **Deploy the monitoring stack** — this task always reports `changed`, on every run, regardless of whether anything actually changed. It's implemented with the generic `command` module (`docker compose up -d`), which has no way to inspect whether Docker actually created/recreated anything — it only knows whether the command executed. The underlying `docker compose up -d` itself *is* idempotent (it won't recreate already-running containers), but Ansible can't see that through a raw shell-out. Using the `community.docker.docker_compose_v2` module instead would report accurate change status; noted here as a known gap rather than something to quietly ignore.

## Validation

I ran both playbooks against the lab, then ran `node_exporter.yml` again to check whether it made any more changes:

![Ansible node_exporter playbook idempotent re-run](../screenshots/monitoring/ansible-node-exporter-idempotent.png)

The `PLAY RECAP` shows `changed=0` across all three nodes on the second run, with the download/extract/install tasks all reporting `skipping` since `node_exporter` was already present — showing that this second run did not download or restart anything. This checks the installed state I tested; it does not prove every upgrade or recovery case. Prometheus's **Status → Targets** page continued to show all `proxmox-nodes` targets `UP` throughout.
