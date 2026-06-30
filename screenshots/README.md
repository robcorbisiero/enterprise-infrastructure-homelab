# Screenshot Evidence

This folder contains validation screenshots for the homelab build.

## Gallery

### Cisco Switching

| Screenshot | Evidence |
| --- | --- |
| ![Cisco show version](networking/cisco-show-version.png) | Cisco Catalyst 3750 IOS and boot image validation. |
| ![Cisco VLAN brief](networking/cisco-vlan-brief.png) | VLANs including `Corporate_Data`, `IoT_Devices`, `Guest_Network`, and `WAN`. |
| ![Cisco IP interface brief](networking/cisco-ip-interface-brief.png) | SVI addressing for VLAN 10 and VLAN 20. |
| ![Cisco VTY SSH config](networking/cisco-vty-ssh-config.png) | VTY lines configured for `login local` and `transport input ssh`. |

### Windows Server

| Screenshot | Evidence |
| --- | --- |
| ![Server Manager dashboard](windows-server/server-manager-dashboard.png) | Windows Server roles installed and manageable. |
| ![Active Directory](windows-server/active-directory-domain-controllers.png) | Domain controller object under `lab.corbitpros.com`. |
| ![DNS Manager](windows-server/dns-forward-lookup-zones.png) | AD-integrated DNS zones. |
| ![DHCP scope](windows-server/dhcp-active-scope.png) | Active DHCP scope for `10.10.10.0`. |
| ![WDS configured](windows-server/wds-configured.png) | Windows Deployment Services configured in native mode. |

### Proxmox, Linux, and Docker

| Screenshot | Evidence |
| --- | --- |
| ![Proxmox cluster summary](proxmox/cluster-summary.png) | `Corbit-Cloud` Proxmox cluster with three online nodes. |
| ![Ubuntu VM summary](proxmox/ubuntu-vm-summary.png) | Ubuntu Server VM running on Proxmox. |
| ![Ubuntu SSH hostnamectl](linux/ubuntu-ssh-hostnamectl.png) | SSH login to `ubuntu-srv-01` and Ubuntu host details. |
| ![Ubuntu SSH service status](linux/ubuntu-ssh-service-status.png) | SSH service active and accepting public key sessions. |
| ![Portainer container running](docker/portainer-container-running.png) | Docker and Portainer running on the Ubuntu server. |
