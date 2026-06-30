# Enterprise Infrastructure Homelab

<img src="photos/homelab-setup-01.jpg" alt="Enterprise infrastructure homelab setup with Cisco switch, Windows Server host, and mini PCs" width="760">

## Overview

This repository documents an enterprise-style infrastructure homelab built from refurbished desktop hardware, a Cisco Catalyst switch, Windows Server, Proxmox VE, Ubuntu Server, Active Directory, DHCP, DNS, WDS, SSH, Docker, Portainer, and shared storage.

The purpose of the lab is to build and prove practical systems administration skills: recovering hardware, configuring switching, deploying directory services, building virtualization infrastructure, troubleshooting routing, and documenting the work in a professional format.

## Current Architecture

- Cisco Catalyst 3750G switch used as the core lab switch.
- Windows Server 2016 host used for lab infrastructure roles.
- Active Directory domain based on `corbitpros.com`.
- DNS and DHCP configured on Windows Server.
- Windows Deployment Services prepared for imaging mini PCs.
- Three Proxmox VE mini PCs clustered together.
- Windows NFS shared storage added to Proxmox from pooled SSD storage.
- Ubuntu Server VM deployed inside Proxmox.
- SSH key-based authentication configured for the Ubuntu VM.
- Docker installed on Ubuntu Server with Portainer running for container management.
- Physical setup and DIY console cable photos are archived in [photos](photos/).
- Validation screenshots are archived in [screenshots](screenshots/).

## Network Design

| Segment | Purpose | Example Addressing |
| --- | --- | --- |
| VLAN 1 | Initial switch management and recovery access | Existing/home-network dependent |
| VLAN 10 | Core lab/server network | `10.10.10.0/24` |
| VLAN 20 | Secondary/IoT-style test network | `10.20.20.0/24` |
| Router uplink | Internet access during build phases | Home router dependent |

The network started as a simple router-to-switch connection, then evolved into a routed lab network with Windows Server providing core infrastructure services and Proxmox hosts connected through the Cisco switch.

## Topology

![Enterprise homelab topology](diagrams/homelab-topology.svg)

## Physical Build

| Homelab setup | DIY console cable |
| --- | --- |
| <img src="photos/homelab-setup-01.jpg" alt="Homelab setup with monitor, Cisco switch, server hardware, and mini PCs" width="360"> | <img src="photos/diy-console-cable-01.jpg" alt="Temporary DIY console cable connected to a StarTech USB to DB9 adapter" width="360"> |

Additional photos are available in [photos](photos/).

## Major Build Phases

### 1. Cisco Switch Recovery

Recovered a Cisco Catalyst 3750G from the `switch:` bootloader prompt using a temporary handmade serial console connection, PuTTY, flash inspection, BOOT variable cleanup, and manual IOS boot commands.

![DIY Cisco console cable pinout](diagrams/diy-console-cable-pinout.svg)

Documentation:

- [Cisco switch recovery](networking/cisco-switch-recovery.md)
- [Serial console access](networking/switch-console-access.md)
- [Cisco 3750G lab configuration](networking/cisco-3750g-lab.md)

### 2. Switching and VLAN Labs

Configured switch management access, restored SSH after a port/VLAN change caused a management lockout, created VLANs, assigned access ports, and began using the Catalyst switch as the core lab network device.

### 3. Windows Server Infrastructure

Installed Windows Server 2016 after newer server driver compatibility issues with an older Wi-Fi card. Configured the server for infrastructure roles including Active Directory Domain Services, DNS, DHCP, and WDS preparation.

![Windows Server Manager dashboard](screenshots/windows-server/server-manager-dashboard.png)

Documentation:

- [Windows Server 2016 install](windows-server/windows-server-2016-install.md)
- [Active Directory](windows-server/active-directory.md)
- [Windows Deployment Services](windows-server/windows-deployment-services.md)

### 4. Proxmox Cluster and Shared Storage

Installed Proxmox VE on mini PCs, created a three-node cluster, configured the no-subscription repository path, and attached Windows NFS shared storage backed by pooled SSDs.

![Proxmox cluster summary](screenshots/proxmox/cluster-summary.png)

Documentation:

- [Proxmox VM inventory](proxmox/vm-inventory.md)
- [Windows NFS shared storage](proxmox/windows-nfs-storage.md)

### 5. Ubuntu Server and Secure Administration

Created an Ubuntu Server VM, fixed routing/DNS issues through the Windows Server gateway path, generated SSH keys, configured passwordless login, and hardened SSH by disabling password authentication.

![Portainer running on Docker](screenshots/docker/portainer-container-running.png)

Documentation:

- [Ubuntu Server VM](proxmox/ubuntu-server-vm.md)
- [Docker host setup](proxmox/docker-host.md)

## Skills Demonstrated

- Cisco console recovery and bootloader troubleshooting
- Serial pinout troubleshooting and PuTTY access
- Cisco IOS switching configuration
- VLAN planning and port assignment
- Layer 3 network troubleshooting
- Windows Server installation and driver validation
- Active Directory Domain Services
- DNS and DHCP administration
- WDS imaging preparation
- Proxmox VE installation and clustering
- Proxmox shared storage integration
- Windows NFS storage pooling and export concepts
- Ubuntu Server administration
- SSH key generation, public key deployment, and daemon hardening
- Docker, Docker Compose, and Portainer deployment
- Docker repository and GPG-key troubleshooting
- Technical documentation and root cause analysis

## Repository Structure

- [networking](networking/) - Cisco switch recovery, console access, VLANs, and routing notes.
- [windows-server](windows-server/) - Windows Server install, AD DS, DNS, DHCP, and WDS notes.
- [proxmox](proxmox/) - Proxmox cluster, Windows NFS shared storage, VM inventory, Ubuntu Server, and Docker notes.
- [diagrams](diagrams/) - Pinout and architecture diagrams.
- [photos](photos/) - Homelab setup and DIY console cable photos.
- [screenshots](screenshots/) - Validation screenshots for networking, Windows Server, Proxmox, Linux, and Docker.
- [hardware-inventory.md](hardware-inventory.md) - Physical equipment used in the lab.
- [troubleshooting-log.md](troubleshooting-log.md) - Issues encountered and how they were resolved.
- [lessons-learned.md](lessons-learned.md) - Practical takeaways from the build.

## Portfolio Goal

This homelab is designed to demonstrate job-ready infrastructure skills for help desk, junior systems administrator, network support, data center technician, and infrastructure operations roles.
