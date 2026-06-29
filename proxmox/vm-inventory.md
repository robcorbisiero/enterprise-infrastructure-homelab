# Proxmox VM and Node Inventory

## Proxmox Cluster

| Item | Status |
| --- | --- |
| Cluster type | Three-node Proxmox VE cluster |
| Nodes | Three mini PCs currently connected |
| Shared storage | Ceph configured |
| Expansion | Additional mini PCs available for imaging or future nodes |

## Virtual Machines

| VM | Role | Notes |
| --- | --- | --- |
| Ubuntu Server | Linux administration and Docker host | Used for SSH hardening, routing validation, and Docker setup. |

## Operational Notes

- Proxmox was installed directly on mini PC hardware.
- The nodes were joined into a cluster.
- Ceph was configured to practice shared storage concepts.
- The lab was managed through the Proxmox web interface and from the Windows Server environment.

## Next Inventory Improvements

Add the following details as the lab matures:

- Node hostnames
- Node management IP addresses
- CPU/RAM/storage per node
- VM IDs
- VM vCPU/RAM/disk allocations
- Ceph OSD disk details
