# Proxmox VM and Node Inventory

## Proxmox Cluster

| Item | Status |
| --- | --- |
| Cluster type | Three-node Proxmox VE cluster |
| Nodes | `pve-01`, `pve-02`, `pve-03` |
| Node IPs | `10.10.10.11`, `10.10.10.12`, `10.10.10.13` |
| Node hardware | Intel i3-6100T, 8 GB RAM, 64 GB NVMe (each node) |
| Shared storage | `Windows-NFS` mounted from Windows Server pooled SSD storage |
| Expansion | Additional mini PCs available for imaging or future nodes |

## Virtual Machines

| VM | VMID | Node | vCPU | RAM | Disk | Role |
| --- | --- | --- | --- | --- | --- | --- |
| `ubuntu-srv-01` | 100 | `pve-02` | 1 | 1 GiB | 15 GiB (raw, on `Windows-NFS`) | Linux administration and Docker host. Used for SSH hardening, routing validation, and Docker setup. |

## Shared Storage Detail

| Item | Value |
| --- | --- |
| Storage name | `Windows-NFS` |
| Type | NFS |
| Content | Disk image, ISO image, Container template |
| Total size | 188.84 GB |
| Used | 19.63 GB (10.39%) |

## Operational Notes

- Proxmox was installed directly on mini PC hardware.
- The nodes were joined into a cluster.
- Windows NFS shared storage was added to the cluster from pooled SSD storage on the Windows Server host.
- The lab was managed through the Proxmox web interface and from the Windows Server environment.

## Next Inventory Improvements

Add the following details as the lab matures:

- Windows NFS export path and storage pool details on the Windows Server side
- Additional VMs/containers as they're deployed
