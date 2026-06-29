# Proxmox Ceph Cluster

## Objective

Build a small Proxmox VE cluster using mini PCs and configure Ceph to learn shared storage, clustering, and high-availability infrastructure concepts.

## Current State

- Three mini PCs are plugged in and clustered.
- Ceph has been configured.
- Additional mini PCs are available for future expansion.

## Why Ceph

Ceph provides a realistic way to practice:

- Distributed storage concepts
- Replication
- Cluster quorum thinking
- Shared VM storage
- Storage troubleshooting
- Enterprise virtualization vocabulary

## Build Notes

During setup, the Proxmox no-subscription repository path was used so packages could be installed without an enterprise subscription.

The lab also included discussion around storage layout, provisioning type, size choices, and volume naming. Because this is a learning homelab, the emphasis was placed on simple, understandable configuration that can be documented clearly.

## Validation Checklist

- Confirm all three nodes appear in the Proxmox cluster.
- Confirm cluster quorum is healthy.
- Confirm Ceph services are visible.
- Confirm storage appears in the Proxmox UI.
- Create or migrate a small test VM when ready.

## Future Work

- Document node names and IP addresses.
- Add OSD disk inventory.
- Capture Ceph health output.
- Test VM migration between nodes.
- Add screenshots of the cluster and storage views.
