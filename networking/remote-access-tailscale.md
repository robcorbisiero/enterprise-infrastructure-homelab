# Remote Access with Tailscale

## Objective

Reach the lab from outside the house without exposing anything to the internet — no
port forwarding on the home router, no RDP or SSH published to a public IP, and no
inbound firewall rules to maintain.

## Approach

[Tailscale](https://tailscale.com/) is a mesh VPN built on WireGuard. Devices
authenticate to a private network (a *tailnet*) and connect directly to each other
over encrypted tunnels, each getting a stable `100.x.y.z` address. Because every
connection is outbound to the coordination service, nothing needs to be opened on the
home router.

In this lab Tailscale is installed on two machines:

| Device | Role in the tailnet |
| --- | --- |
| Windows Server 2016 host | The lab-side node — the machine that gets remoted into |
| Personal PC | The client used to connect from outside the lab network |

The Windows Server host is the natural entry point: it already sits on the lab network
(`10.10.10.0/24`) and already serves as the administrative jump-off point for the rest
of the lab, so reaching it remotely is enough to reach everything behind it.

## Why the Windows Server Is the Entry Point

The rest of the lab is reached by hopping through the Windows Server host once
connected, which mirrors how the lab was already being administered locally:

- Proxmox web UIs on `10.10.10.11-13:8006`
- The Ubuntu Docker host over SSH (`ssh administrator@10.10.10.103`), as in
  [Ubuntu Server VM](../proxmox/ubuntu-server-vm.md)
- The monitoring stack UIs on `10.10.10.103` (Prometheus `:9090`, Grafana `:3000`,
  Alertmanager `:9093`), as in [Monitoring and Alerting](../monitoring/monitoring-stack.md)
- AD, DNS, and DHCP management consoles on the server itself

This is a jump-host pattern: one authenticated entry point into the network rather
than several separately exposed services.

## Setup

On the Windows Server host:

1. Install the Tailscale Windows client.
2. Sign in to authenticate the machine into the tailnet.
3. Confirm the device appears in the Tailscale admin console with a `100.x` address.

On the personal PC, install the client and sign in to the same tailnet. Both devices
then appear as peers and can address each other directly.

Remote Desktop to the Windows Server host over its tailnet address rather than its
LAN address, and the session works identically from inside or outside the house.

## Validation

- Both devices show as **Connected** in the Tailscale admin console.
- From the personal PC on a network outside the lab (e.g. a phone hotspot), Remote
  Desktop to the Windows Server host's `100.x` address succeeds.
- The home router has **no** inbound port forwarding rules for RDP, SSH, or any lab
  service — remote access works purely through the tailnet.

## Security Notes

- Nothing is published to the public internet; there is no inbound attack surface on
  the home router to harden or monitor.
- Traffic between devices is WireGuard-encrypted end to end.
- Access is tied to device authentication in the tailnet, so revoking a lost or
  retired device is done in the admin console rather than by rotating a shared secret.
- The domain password and lockout policy from
  [Organizational Units and Group Policy](../windows-server/group-policy.md) still
  applies to the Remote Desktop logon itself — the VPN controls who can *reach* the
  host, not who can *log into* it.

## Planned Enhancements

- Advertise `10.10.10.0/24` from a subnet router so lab hosts are reachable directly
  by their LAN addresses, instead of only by hopping through the Windows Server host.
- Enable MagicDNS so devices can be reached by name rather than `100.x` address.
- Define tailnet ACLs to restrict which devices can reach which lab services, rather
  than allowing any tailnet member to reach everything.

## Skills Demonstrated

- Mesh VPN (WireGuard) deployment for remote administration
- Secure remote access design without inbound port forwarding
- Jump-host access patterns into a segmented lab network
- Reducing external attack surface as an alternative to exposing services
