# Troubleshooting Log

## Cisco Switch Entered `switch:` Bootloader Prompt

**Symptom:** PuTTY connected successfully, but the switch stopped at the `switch:` prompt instead of normal IOS.

**Cause:** The switch was in bootloader/recovery mode and could not complete the normal boot path automatically.

**Actions Taken:**

- Built temporary serial access from a StarTech USB-to-DB9 adapter, Ethernet cable, and Dupont wires.
- Opened PuTTY on Windows 11 using the detected COM port.
- Used standard serial settings: 9600 baud, 8 data bits, no parity, 1 stop bit, no flow control.
- Ran bootloader commands including `flash_init`, `dir flash:`, `unset BOOT`, and manual `boot flash:/...`.

**Resolution:** Switch booted into IOS and reached the default `Switch>`/enable workflow.

## Manual IOS Boot Returned `invalid argument`

**Symptom:** Boot command attempted to load `c3750-ipbasek9-mz.122-55.SE4.bin` but returned `invalid argument`.

**Cause:** The bootloader had trouble parsing the IOS path.

**Actions Taken:**

- Inspected flash contents.
- Cleared stale BOOT variables.
- Retried the boot with a full flash path.

**Resolution:** The switch successfully booted after correcting the boot path and environment.

## Lost SSH After Reconfiguring Port 1

**Symptom:** SSH access to the switch was lost after configuring port 1 while it was connected to the router.

**Cause:** The active management path depended on the same uplink/port being changed.

**Actions Taken:**

- Used console/local access to recover.
- Restored the management path.
- Confirmed SSH returned before continuing VLAN work.

**Resolution:** SSH management access was restored.

## Windows Server Had No Internet Through Lab Switch

**Symptom:** Windows Server connected to port 2 on the switch but had no internet access. Initial pings from the switch to `10.10.10.2` failed.

**Cause:** The lab network path, server addressing, firewall, and gateway assumptions needed to be aligned.

**Actions Taken:**

- Verified switch port placement.
- Checked server IP configuration.
- Tested ping from the switch to the server.
- Adjusted the setup until the switch could successfully ping the server.

**Resolution:** Switch-to-server connectivity was restored. Internet routing required additional design work because the server used Wi-Fi for upstream access and Ethernet for the lab network.

## Wi-Fi Adapter Driver Failed on Newer Windows Server

**Symptom:** Wi-Fi card appeared under Other devices as Network Controller/Unknown Device and showed a yellow warning triangle. Driver attempts resulted in Code 39.

**Cause:** The older Wi-Fi adapter from a Gateway DX4860 did not work cleanly with the newer Windows Server version.

**Actions Taken:**

- Checked Device Manager.
- Reviewed hardware ID details.
- Tried driver installation.
- Downgraded the server OS.

**Resolution:** Windows Server 2016 recognized the Wi-Fi card and allowed the lab build to continue.

## DHCP Authorization/Visibility Issue

**Symptom:** DHCP did not initially appear as expected, and the server showed APIPA behavior during setup.

**Cause:** DHCP authorization and interface placement needed to be checked in the Windows Server console.

**Actions Taken:**

- Reviewed the authorized DHCP servers list.
- Confirmed the DHCP server was already authorized.
- Rechecked the server role state.

**Resolution:** DHCP appeared correctly in the management list and began working.

## Ubuntu VM Routing/DNS Issues

**Symptom:** Ubuntu VM networking did not work correctly until routing and DNS were revisited.

**Cause:** The VM needed the correct gateway and nameserver behavior for the Windows Server/NAT-style lab path.

**Actions Taken:**

- Reviewed default gateway settings.
- Checked Windows routing behavior.
- Updated the Ubuntu network configuration.
- Forced network configuration refresh.

**Resolution:** Connectivity began working after the routing/DNS path was corrected.

## SSH Service Name on Ubuntu

**Symptom:** `sudo systemctl restart sshd` returned `sshd.service not found`.

**Cause:** Ubuntu/Debian commonly uses the service name `ssh`.

**Actions Taken:**

- Restarted the service with `sudo systemctl restart ssh`.
- Verified service state with `sudo systemctl status ssh`.

**Resolution:** SSH service restarted correctly and key-based access continued to work.

## Docker Repository GPG Key Error

**Symptom:** `sudo apt update` returned `NO_PUBKEY 7EA0A9C3F273FCD8` for the Docker Ubuntu repository, and Docker packages could not be located.

**Cause:** The Docker repository file existed, but the GPG key was missing, unreadable, or not located at the path referenced by `signed-by=`.

**Actions Taken:**

- Identified an earlier typo using `sources.list.y` instead of `sources.list.d`.
- Recreated the Docker repository file.
- Attempted to place Docker's GPG key under `/etc/apt/keyrings/`.
- Switched the plan toward using a readable `.asc` key path.

**Current Status:** Docker installation was in progress. The next validation step is a clean `sudo apt update`, followed by installing `docker-ce`, `docker-ce-cli`, `containerd.io`, and `docker-compose-plugin`.
