# Proxmox VM and Node Inventory

## Proxmox Cluster

| Item | Status |
| --- | --- |
| Cluster type | Three-node Proxmox VE cluster |
| Nodes | `pve-01`, `pve-02`, `pve-03` |
| Node IPs | `10.10.10.11`, `10.10.10.12`, `10.10.10.13` |
| Shared storage | `Windows-NFS` mounted from Windows Server pooled SSD storage |
| Expansion | Additional mini PCs available for imaging or future nodes |

## Virtual Machines

| VM | Role | Notes |
| --- | --- | --- |
| Ubuntu Server | Linux administration and Docker host | Used for SSH hardening, routing validation, and Docker setup. |

## Operational Notes

- Proxmox was installed directly on mini PC hardware.
- The nodes were joined into a cluster.
- Windows NFS shared storage was added to the cluster from pooled SSD storage on the Windows Server host.
- The lab was managed through the Proxmox web interface and from the Windows Server environment.

## Next Inventory Improvements

Add the following details as the lab matures:

- Node hostnames
- Node management IP addresses
- CPU/RAM/storage per node
- VM IDs
- VM vCPU/RAM/disk allocations
- Windows NFS export path and storage pool details
