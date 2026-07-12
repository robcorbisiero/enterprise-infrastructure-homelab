# Hardware Inventory

## Network Equipment

| Device | Role | Notes |
| --- | --- | --- |
| Cisco Catalyst 3750G | Core lab switch | Recovered from `switch:` bootloader prompt and reused for VLAN/switching labs. |
| Home router | Internet edge | Used as upstream internet during build and troubleshooting. |
| Cisco Meraki MR36 | Wireless access point | Available lab hardware for future wireless/networking work. |

## Servers and Compute

| Device | Role | Notes |
| --- | --- | --- |
| Custom desktop build | Windows Server host | Intel i5-4670K, 16 GB DDR3 RAM, 256 GB SSD. Used for Windows Server infrastructure roles. |
| Mini PCs | Proxmox cluster nodes | Three nodes (Intel i3-6100T, 8 GB RAM, 64 GB NVMe each) currently clustered in Proxmox. Additional mini PCs are available for imaging and expansion. |
| Dell OptiPlex systems | Refurbished lab clients/hosts | Used for hardware practice and potential client imaging. |
| Lenovo ThinkCentre systems | Refurbished lab clients/hosts | Used for hardware practice and potential client imaging. |

## Adapters and Components

| Item | Use |
| --- | --- |
| StarTech USB-A to DB9 serial adapter | Connected Windows 11 laptop to Cisco console using PuTTY. |
| Ethernet patch cable | Modified as part of the temporary Cisco console connection. |
| Dupont connector wires | Wrapped around DB9 male pins to create a temporary 3-wire console pinout. |
| Older Wi-Fi card from Gateway DX4860 | Tested in the Windows Server host; worked after downgrading to Windows Server 2016. |
| SSDs, RAM, GPUs, NICs, power supplies | Refurbishment, testing, and lab expansion parts. |

## Current Lab Notes

- The Windows Server host uses Wi-Fi for upstream internet and Ethernet for the lab switch connection.
- Three mini PCs are currently connected and clustered in Proxmox.
- Three SSDs are pooled on the Windows Server storage host and exported to Proxmox through Windows NFS.
- Additional mini PCs can be imaged through WDS once ISO/install images are added.
- Physical photos of the setup and temporary console cable are stored in [photos](photos/).
