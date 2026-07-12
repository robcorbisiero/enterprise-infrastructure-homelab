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

**Resolution:** Docker installation was completed after correcting the repository/key workflow. Portainer was deployed successfully and is running on the Ubuntu server at `10.10.10.103` using HTTPS port `9443`.

## Stale DNS Record After Renaming a Domain-Joined Client

**Symptom:** After renaming the WDS-imaged Windows 10 client to `PC01`, the computer object couldn't be cleanly located/moved into the new `Workstations` OU under its new name.

**Cause:** Renaming a domain-joined machine doesn't immediately update its DNS `A` record — the old name/record lingered until the client re-registered itself.

**Actions Taken:**

- Ran `ipconfig /flushdns` on the client to clear the stale local resolver cache.
- Ran `ipconfig /registerdns` on the client to force re-registration of its DNS record under the new name.

**Resolution:** DNS reflected the new `PC01` name, after which the computer object was correctly identifiable for the `Move-ADObject` step into the `Workstations` OU.

## Workstations Policy User Settings Not Applying

**Symptom:** `gpresult /r` on `PC01` showed `Workstations Policy` applied under COMPUTER SETTINGS, but USER SETTINGS showed `N/A` — the drive map and screensaver policy never reached the logged-in user, despite the GPO being correctly linked and enabled.

**Cause:** `User Configuration` settings apply based on the OU containing the *user* object, not the computer. `LAB\Administrator` lives in the default `Users` container, not the `Workstations` OU, so the GPO's user-side settings were never evaluated for that logon.

**Actions Taken:**

- Confirmed via `gpresult /r` that the GPO was linked, enabled, and applied on the computer side but absent from user-side processing.
- Enabled Group Policy Loopback Processing (Merge mode) on `Workstations Policy` under `Computer Configuration → Policies → Administrative Templates → System → Group Policy`.
- Ran `gpupdate /force` and re-checked with `gpresult /r`.

**Resolution:** `Workstations Policy` appeared in both the Computer Settings and User Settings applied lists after enabling loopback processing, and the mapped drive appeared correctly on `PC01`.

## Proxmox Backup Job Writing to Local Storage Instead of PBS

**Symptom:** A backup job for `ubuntu-srv-01` (VMID 100) completed with `OK` status in the task log, but the Proxmox Backup Server datastore showed `0 Groups, 0 Snapshots` — the backup didn't actually reach PBS.

**Cause:** The completed backups were `.vma.zst` files on the VM's `local` storage, not PBS's format. The backup job/dialog had defaulted to `local` for the Storage field instead of the intended `pbs-backup` target.

**Actions Taken:**

- Confirmed the mismatch by checking the VM's Backup tab (showed backups on `local`) against the PBS Content tab (showed nothing).
- Re-ran the backup via **Backup now**, explicitly selecting `pbs-backup` as the Storage target.

**Resolution:** The PBS datastore then showed a `vm/100` backup group with a snapshot, confirmed by a subsequent test restore to a new VMID.
