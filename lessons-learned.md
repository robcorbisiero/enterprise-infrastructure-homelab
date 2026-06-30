# Lessons Learned

## Hardware and Physical Access

- A missing official console cable does not stop recovery work if the serial pinout is understood and tested carefully.
- DB9 serial, USB, and RJ45 console wiring are not interchangeable; the signals and pinouts matter.
- Dupont connector wires can be useful for temporary lab access, but a proper console cable is the right long-term tool.
- Older hardware can be very useful for learning, but driver support often decides which operating system version is realistic.

## Cisco Switching

- The `switch:` prompt means the switch is in bootloader mode and IOS has not fully loaded.
- `flash_init`, `dir flash:`, `unset BOOT`, and manual `boot flash:/...` commands are important recovery tools.
- Management access can be lost quickly when changing uplink ports, VLAN assignments, or gateway assumptions.
- Keeping console access available while changing switch management settings prevents unnecessary lockouts.

## Windows Server

- Windows Server 2025 created driver friction with older wireless hardware.
- Windows Server 2016 was a better fit for the older Wi-Fi adapter and allowed the lab to continue.
- AD DS, DNS, DHCP, and WDS create a strong portfolio story because they show centralized identity, name resolution, addressing, and deployment.
- When a server has Wi-Fi for internet and Ethernet for the lab switch, routing and default gateway choices must be deliberate.

## Proxmox and Virtualization

- A three-node Proxmox cluster is a realistic way to learn enterprise virtualization concepts on small hardware.
- Shared storage design should match the hardware. Windows NFS was a better current fit than Ceph because the SSDs are pooled on the Windows Server storage host.
- Static addressing and consistent host naming are important before adding nodes to a cluster.

## Linux Administration

- On Ubuntu/Debian systems, the SSH service is commonly managed as `ssh`, not `sshd`.
- SSH key authentication should be tested in a second session before closing the original session.
- Disabling password authentication is a meaningful hardening step after key-based login is confirmed.
- Docker installation failures often come from repository files, GPG key placement, or package source trust problems.

## Documentation

- Troubleshooting steps are valuable portfolio material when they show the symptom, root cause, fix, and validation.
- Recruiters and hiring managers do not need a raw transcript; they need clear summaries of what was built and what skills were demonstrated.
