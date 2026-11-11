# Homelab portfolio: evidence and technical walkthrough

This page is a quick guide for recruiters, hiring managers, and engineers reviewing the [full homelab README](README.md). It links to documentation and screenshots from a historical build. The repository does not establish that all services remain active today.

## Selected demonstrations

| Competency | What was implemented or tested | Evidence |
| --- | --- | --- |
| Layer 2 networking | Recovered a Cisco Catalyst 3750G from the boot loader and documented VLAN and access issues | [Switch recovery](networking/cisco-switch-recovery.md), [Switch lab](networking/cisco-3750g-lab.md) |
| Windows infrastructure | Set up AD DS, DNS, DHCP, and Windows Deployment Services; PXE-imaged and domain-joined a Windows 10 client | [AD documentation](windows-server/active-directory.md), [WDS](windows-server/windows-deployment-services.md) |
| Group Policy | Troubleshot user settings that required loopback processing | [Group Policy](windows-server/group-policy.md) |
| Virtualization and storage | Built a three-node Proxmox cluster with Windows-hosted pooled SSD storage shared over NFS | [VM inventory](proxmox/vm-inventory.md), [NFS setup](proxmox/windows-nfs-storage.md) |
| Linux and containers | Configured Ubuntu SSH access, Docker, and Portainer | [Ubuntu VM](proxmox/ubuntu-server-vm.md), [Docker host](proxmox/docker-host.md) |
| Monitoring | Configured Prometheus, Grafana, Alertmanager, exporters, and cAdvisor; checked scrape targets | [Monitoring documentation](monitoring/monitoring-stack.md), [Targets screenshot](screenshots/monitoring/prometheus-targets-all-up.png) |
| Automation | Created Ansible playbooks; recorded a second exporter run with zero changes and separately documented untested corrections to the Docker playbook | [Ansible notes](ansible/automation.md) |
| Backup validation | Corrected a backup destination mistake and restored a PBS snapshot to a separate bootable VM | [Backup and restore test](proxmox/backup-and-disaster-recovery.md) |

## Architecture

[View topology](diagrams/homelab-topology.svg) or [PNG version](diagrams/homelab-topology.png).

The lab used a Cisco 3750G, a Windows Server 2016 desktop, three Proxmox mini PCs, and shared storage exported over NFS. **NFS is not Ceph**, and the backup server shared the same cluster failure domain.

## Troubleshooting examples

- **Switch recovery:** boot loader recovery and management connectivity.
- **Windows policy:** diagnosing computer-versus-user Group Policy application.
- **Linux packages:** resolving a Docker repository signing-key problem.
- **Backup reliability:** distinguishing a successful job from a backup stored in the intended datastore, followed by a restore test.

Read the [full troubleshooting log](troubleshooting-log.md) for commands, context, and outcomes.

## Scope and limitations

- The screenshots and notes capture tests at the time, not a current production service-level guarantee.
- The PBS restore confirmed boot and container services, but not resilience to loss of the entire cluster.
- Monitoring notification delivery was not validated with a real Alertmanager receiver.
- Some revised automation code still requires a fresh-host test.
- Remote access requires additional external-network validation evidence.

## Security and reproducibility

This is an educational lab, not a production-ready deployment guide. Before reusing configurations, replace hostnames, addresses, credentials, keys, and notification endpoints; review firewall boundaries and software versions. Do not publish private keys, passwords, tokens, or personally identifying network information.

## Suggested review path

1. [README and hardware overview](README.md)
2. [Network topology](diagrams/homelab-topology.svg)
3. [AD and deployment](windows-server/active-directory.md)
4. [Proxmox and NFS](proxmox/windows-nfs-storage.md)
5. [Monitoring and Ansible](ansible/automation.md)
6. [Backup restore and troubleshooting](proxmox/backup-and-disaster-recovery.md)
