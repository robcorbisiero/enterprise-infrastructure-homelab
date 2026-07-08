# Monitoring and Alerting Stack

## Objective

Add centralized monitoring and alerting for the lab: the Ubuntu Docker host, its containers, and the three Proxmox VE nodes, using Prometheus, Grafana, node_exporter, cAdvisor, and Alertmanager.

## Architecture

| Component | Role | Where it runs |
| --- | --- | --- |
| Prometheus | Metrics collection and storage | Container on the Ubuntu Docker host |
| Alertmanager | Alert routing/notification | Container on the Ubuntu Docker host |
| Grafana | Dashboards | Container on the Ubuntu Docker host |
| node-exporter (container) | Host metrics for the Docker VM | Container on the Ubuntu Docker host |
| cAdvisor | Per-container metrics (CPU, memory, network) | Container on the Ubuntu Docker host |
| node_exporter (native) | Host metrics for each Proxmox node | Installed directly on `pve-01`, `pve-02`, `pve-03` |

Proxmox VE hosts don't run the Docker stack themselves, so `node_exporter` is installed as a native binary/service on each PVE node rather than as a container, and Prometheus scrapes each node directly over the lab network (`10.10.10.11-13:9100`).

## Deployment Path

Files live in [monitoring](.) alongside this doc:

- `docker-compose.yml` - Prometheus, Alertmanager, Grafana, node-exporter, and cAdvisor stack
- `prometheus.yml` - scrape targets and alerting integration
- `alert.rules.yml` - alerting rules (node down, high CPU, low disk, high memory)
- `alertmanager.yml` - alert routing (default receiver is a placeholder; add real notification config before relying on it)
- `grafana/provisioning/datasources/prometheus.yml` - auto-provisions Prometheus as the default Grafana datasource

On the Ubuntu Docker host:

```bash
cd monitoring
docker compose up -d
```

On each Proxmox node (`pve-01`, `pve-02`, `pve-03`):

```bash
useradd --no-create-home --shell /usr/sbin/nologin node_exporter
wget https://github.com/prometheus/node_exporter/releases/latest/download/node_exporter-*.linux-amd64.tar.gz
tar xvf node_exporter-*.linux-amd64.tar.gz
cp node_exporter-*/node_exporter /usr/local/bin/
chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

Then create a systemd unit (`/etc/systemd/system/node_exporter.service`) running as the `node_exporter` user, `enable --now` it, and confirm `curl localhost:9100/metrics` returns data before moving to the next node.

## Access Points (once deployed)

```text
Prometheus:   http://10.10.10.103:9090
Alertmanager: http://10.10.10.103:9093
Grafana:      http://10.10.10.103:3000
```

## Validation Steps

- Prometheus **Status > Targets** shows all targets (`docker-host`, `docker-containers`, and all three `proxmox-nodes`) as `UP`.
- Grafana loads with the Prometheus datasource already configured (no manual setup).
- Triggering a test condition (e.g. stopping a Proxmox node's `node_exporter` service) produces a firing alert in Prometheus **Alerts** and a corresponding notification in Alertmanager.

## Planned Enhancements

- Import or build Grafana dashboards for host and container metrics.
- Configure a real Alertmanager receiver (email or webhook) in place of the placeholder.
- Add `windows_exporter` on the Windows Server host for AD/DNS/DHCP visibility.
- Add long-term retention or remote-write if metrics need to outlive local disk space.
