# Cisco Console Access

## Objective

Gain console access to a Cisco Catalyst switch without an official Cisco console cable, using available hardware and a temporary 3-wire serial connection.

## Equipment Used

- Windows 11 laptop
- StarTech USB-A to male DB9 serial adapter
- Standard Ethernet cable
- Dupont connector wires
- PuTTY
- Cisco Catalyst 3750G switch

Photos of the temporary cable and physical setup are stored in [../photos](../photos/).

## Serial Settings

| Setting | Value |
| --- | --- |
| Connection type | Serial |
| Speed | `9600` |
| Data bits | `8` |
| Parity | `None` |
| Stop bits | `1` |
| Flow control | `None` |

## Process

1. Connected the StarTech USB-to-DB9 adapter to the Windows 11 laptop.
2. Confirmed the adapter appeared in Device Manager under Ports (COM & LPT).
3. Used PuTTY to open the detected COM port.
4. Built a temporary serial connection using Dupont connector wires wrapped around the male DB9 pins.
5. Connected the RJ45 end into the Cisco console port.
6. Pressed Enter in PuTTY until the switch responded.

## Important Notes

- The StarTech adapter was not cut open because it contains active USB-to-serial electronics.
- Only the required serial lines were used for the temporary connection.
- Each temporary pin connection was isolated so adjacent DB9 pins would not touch.
- This was a lab recovery method, not a permanent cabling standard.

## Result

PuTTY successfully connected to the Cisco switch and displayed the `switch:` bootloader prompt.
