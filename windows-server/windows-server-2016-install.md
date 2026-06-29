# Windows Server 2016 Installation

## Objective

Install a Windows Server operating system on a refurbished desktop build to provide core infrastructure services for the homelab.

## Hardware

- Intel i5-4670K desktop build
- 16 GB DDR3 RAM
- 256 GB SSD
- Ethernet connected to Cisco switch
- Older Wi-Fi adapter used for upstream internet access

## Operating System Decision

Windows Server 2025 was initially considered and installed, but the older Wi-Fi adapter did not work properly. Device Manager showed a Network Controller/Unknown Device state and driver attempts resulted in Code 39.

The system was downgraded to Windows Server 2016 because it provided better compatibility with the older wireless hardware.

## Network Role

The Windows Server host was configured with two different network paths:

- Wi-Fi: upstream internet/home network access
- Ethernet: lab switch connection

This created a realistic routing and gateway troubleshooting scenario because the lab-facing Ethernet interface and internet-facing Wi-Fi interface needed to be handled carefully.

## Post-Install Tasks

- Installed Windows updates.
- Confirmed Wi-Fi driver functionality.
- Connected Ethernet to the Cisco switch.
- Prepared the server for infrastructure roles.
- Verified switch-to-server connectivity using ping.

## Roles Planned/Installed

- Active Directory Domain Services
- DNS Server
- DHCP Server
- Windows Deployment Services

## Result

Windows Server 2016 became the central infrastructure server for the lab and provided the foundation for identity, name resolution, addressing, and imaging services.
