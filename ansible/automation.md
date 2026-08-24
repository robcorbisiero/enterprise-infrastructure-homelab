# Configuration Automation with Ansible

## Objective

Replace repetitive manual setup steps with idempotent, repeatable Ansible playbooks — starting with the `node_exporter` install that was previously done by hand, one SSH session at a time, across all three Proxmox nodes in [Monitoring and Alerting](../monitoring/monitoring-stack.md).

## Why This, and Why Now

Phase 6 involved SSHing into `pve-01`, `pve-02`, and `pve-03` individually and running the same `useradd` / `wget` / systemd unit steps three times by hand. That's exactly the kind of repetitive, error-prone toil configuration management tools exist to remove. Automating it here demonstrates the "did it manually once, then automated it" progression real infrastructure work follows.

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

[`playbooks/node_exporter.yml`](playbooks/node_exporter.yml) reproduces the manual install from Phase 6, but idempotently — re-running it is a no-op once `node_exporter` is already installed and running, thanks to the `stat` check before downloading/installing:

```bash
cd ansible
ansible-playbook playbooks/node_exporter.yml
```

Unlike the original manual process, the version is pinned (`1.8.2`) as a playbook variable rather than resolved dynamically at install time — intentional, so a re-run of this playbook always produces the same result instead of picking up whatever the latest GitHub release happens to be on a given day.

The install is gated on the *installed version*, not merely on the binary's presence:

```yaml
- name: Check which node_exporter version is currently installed
  command: /usr/local/bin/node_exporter --version
  register: node_exporter_installed
  changed_when: false
  failed_when: false
```

An earlier revision of this playbook gated on `stat` of `/usr/local/bin/node_exporter` instead. That was idempotent, but it also meant the pin could never be *moved* — bumping `node_exporter_version` was a silent no-op, because the binary already existed at that path and every install task skipped. Comparing the reported version instead makes a version bump an actual upgrade, and the binary install task now notifies the restart handler so the service picks the new binary up.

The download is also checksum-verified against the published `sha256sums.txt` for the pinned release, so a tampered or truncated download fails the task rather than being installed:

```yaml
node_exporter_checksum: "sha256:6809dd0b3ec45fd6e992c19071d6b5253aed3ead7bf0686885a51d85c6643c66"
```

## Playbook: `docker_host.yml`

[`playbooks/docker_host.yml`](playbooks/docker_host.yml) reproduces the Docker install from [Docker Host Setup](../proxmox/docker-host.md) and then brings up the monitoring stack from the cloned repo:

This playbook uses the `community.docker` collection, so install the requirements first:

```bash
cd ansible
ansible-galaxy collection install -r requirements.yml
ansible-playbook playbooks/docker_host.yml --ask-become-pass
```

(`--ask-become-pass` because this one runs locally against `ubuntu-srv-01` and needs a `sudo` password rather than SSH key auth.)

![Ansible docker_host playbook run](../screenshots/monitoring/ansible-docker-host-run.png)

`ok=8, changed=2, failed=0`. That recap is from the **original** version of this playbook, and both `changed` tasks turned out to be worth chasing down rather than glossing over — one of them was hiding a real bug.

- **Add Docker apt repository** — the original task built the repo line with `arch={{ ansible_architecture }}`. That fact comes from `uname -m`, which reports **`x86_64`**, but apt expects the *dpkg* architecture, **`amd64`**. So this task didn't just reformat the file created by hand in [Docker Host Setup](../proxmox/docker-host.md) — it overwrote a working `arch=amd64` line with a broken `arch=x86_64` one.

  The failure mode is silent, which is why the recap still shows `failed=0`. `apt update` fetches the repo's `InRelease`, finds no package index for an unknown architecture, skips it, and **exits 0**. The Docker repo then contributes zero installable packages:

  ```text
  arch=x86_64  ->  no candidate from the repo
  arch=amd64   ->  candidate 5:29.7.2-1~ubuntu.24.04~noble
  ```

  The next task, *Install Docker packages*, reported `ok` only because Docker was already installed by hand during Phase 5 — so the playbook appeared to work while never actually being able to install Docker. On a clean host it would have failed with *Unable to locate package docker-ce*. The lesson: a playbook validated only against a host that already has the desired state isn't validated at all.

  The fix resolves the architecture the same way the manual command did, rather than trusting a similarly-named fact:

  ```yaml
  - name: Resolve the dpkg architecture for the apt repository
    command: dpkg --print-architecture
    register: dpkg_architecture
    changed_when: false
  ```

- **Deploy the monitoring stack** — this task always reported `changed`, on every run, regardless of whether anything actually changed. It was implemented with the generic `command` module (`docker compose up -d`), which has no way to inspect whether Docker actually created/recreated anything — it only knows whether the command executed. The underlying `docker compose up -d` itself *is* idempotent (it won't recreate already-running containers), but Ansible can't see that through a raw shell-out. This now uses `community.docker.docker_compose_v2`, which reports accurate change status.

The playbook also no longer assumes the repo has already been cloned to `{{ repo_path }}` by hand — a `git` task ensures it's present before the stack is brought up, so the playbook is reproducible on a host that has never been touched manually.

## Validation

Both playbooks were run against the live environment. `node_exporter.yml` was also re-run a second time immediately after the first to confirm idempotency:

![Ansible node_exporter playbook idempotent re-run](../screenshots/monitoring/ansible-node-exporter-idempotent.png)

The `PLAY RECAP` shows `changed=0` across all three nodes on the second run, with the download/extract/install tasks all reporting `skipping` since `node_exporter` was already present — confirming the playbook is safe to re-run without re-downloading or restarting the service unnecessarily. Prometheus's **Status → Targets** page continued to show all `proxmox-nodes` targets `UP` throughout.

## Skills Demonstrated

- Ansible control node setup and SSH key trust distribution
- Idempotent playbook design (check-before-install patterns)
- Reproducing prior manual sysadmin work as configuration-as-code
- Ansible inventory groups, `ansible_connection=local`, and privilege escalation (`become`)
