# Remote access with Tailscale

The earlier lab notes record Tailscale on the Windows Server host and a personal PC. The idea was to reach the Windows desktop remotely, then manage the lab from there, instead of forwarding RDP or SSH through the home router.

## Access path

```text
Personal PC with Tailscale
          |
          | tailnet connection / RDP
          v
Windows Server host with Tailscale
          |
          | lab-side Ethernet
          v
Cisco switch / 10.10.10.0/24
          +-- Proxmox nodes: 10.10.10.11–13:8006
          +-- Ubuntu SSH: 10.10.10.103
          +-- Monitoring web interfaces
```

This is a jump-host setup. Installing Tailscale on the Windows host does not automatically route the rest of the lab subnet to the personal PC. I would open the lab's management tools from the remote Windows session.

## Setup notes

1. Install Tailscale on the Windows host and personal PC, using a client version supported by each OS.
2. Authenticate both devices to the same tailnet and check their device entries.
3. Allow the intended PC to reach RDP on the Windows host through the tailnet's access policy and Windows Firewall.
4. Connect Remote Desktop to the Windows host's Tailscale address. Normal Windows logon permissions still apply.

No actual tailnet addresses or credentials are stored here. A `100.x` address is a placeholder, not an address to copy into a client.

## Checks still to record

The branch notes did not include screenshots or command output for this setup. Before treating remote access as verified, I need to record:

- Both devices connected in Tailscale.
- A successful RDP session from a separate network, such as a phone hotspot.
- Access to Proxmox and the Ubuntu host from inside that Windows session.
- The home router's forwarding rules, confirming lab RDP/SSH are not published.

Tailscale connections may be direct or relayed depending on the networks involved. Either way, RDP access still needs appropriate device access rules and Windows permissions. See [Tailscale's RDP guide](https://tailscale.com/kb/1095/secure-rdp-windows).

## Later improvements

- Add a subnet router if direct access to lab IPs is needed.
- Use MagicDNS names instead of copying tailnet IPs.
- Limit access to the specific devices and services I use.
