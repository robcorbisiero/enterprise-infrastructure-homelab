# Cisco 3750G Lab Configuration

## Objective

Use the recovered Cisco Catalyst 3750G as the core switching device for the homelab and practice management, VLAN, and basic Layer 3 switching tasks.

## Lab Scenarios Completed

### Scenario 1: Management Access

Configured the switch for remote administration after console recovery.

Key tasks:

- Confirmed IOS boot.
- Entered privileged EXEC mode.
- Prepared the switch for SSH-based management.
- Validated that console access remained available during management changes.

### Scenario 2: VLAN and Port Assignment

Started segmenting the lab network into dedicated VLANs.

Example VLAN plan:

| VLAN | Purpose | Example Network |
| --- | --- | --- |
| VLAN 10 | Lab servers and management | `10.10.10.0/24` |
| VLAN 20 | Secondary/IoT test network | `10.20.20.0/24` |

Operational note:

- Port 1 was initially connected to the home router for internet/uplink access.
- Changing port 1 configuration caused SSH management to drop.
- Access was restored by correcting the management path.

### Scenario 3: Layer 3 Switching Preparation

Used the Catalyst 3750G as a platform for learning Layer 3 switching concepts.

Key tasks:

- Planned switched virtual interfaces (SVIs).
- Reviewed the need for `ip routing` when routing between VLANs locally.
- Tested connectivity between the switch and Windows Server.
- Validated server reachability after correcting addressing and port configuration.

## Validation Commands

Useful commands during this lab:

```text
show ip interface brief
show vlan brief
show running-config
show ip route
ping 10.10.10.2
```

## Lessons Learned

- Management access should be protected before changing uplink or access-port behavior.
- Console access is the recovery path when SSH is lost.
- Layer 2 port membership, SVI state, and host firewall behavior can all affect ping tests.
- The 3750G is useful for learning both switching and basic routing concepts.
