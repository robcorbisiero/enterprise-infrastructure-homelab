# Windows Server 2016 Installation

## What I was doing

I used my i5 desktop as the Windows Server host. The first challenge was getting the older Wi-Fi card working so the lab had upstream internet access.

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

I had to check the routes and gateway settings carefully with those two interfaces. Getting the switch to ping the server did not, by itself, mean the lab had internet access.

## Post-Install Tasks

- I installed Windows updates.
- I confirmed Wi-Fi driver functionality.
- I connected Ethernet to the Cisco switch.
- I prepared the server for infrastructure roles.
- I verified switch-to-server connectivity using ping.

## Roles I installed

- Active Directory Domain Services
- DNS Server
- DHCP Server
- Windows Deployment Services

## How it turned out

Windows Server 2016 became the central infrastructure server for the lab and provided the foundation for identity, name resolution, addressing, and imaging services.
