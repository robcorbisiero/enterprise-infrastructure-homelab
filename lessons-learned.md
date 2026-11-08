# What I learned

## Keep a way back in

I lost SSH to the Cisco switch while changing the port I was using for management. Having the console connection available let me recover without starting over. I now check how I'm connected before changing ports or VLANs.

The homemade cable got me through the recovery, but it was temporary. Understanding the DB9/RJ45 pinout mattered more than having the right-looking connector.

## Let the hardware decide some of the design

The older Wi-Fi card wouldn't cooperate with Server 2025. Server 2016 worked, which let me get on with the lab. That was a hardware compatibility decision, not a recommendation for a new deployment.

I also went with Windows NFS instead of Ceph because the SSDs were attached to the Windows box, not spread across the Proxmox nodes. Shared storage worked for this setup, but it also made that Windows host a dependency for the VMs using it.

## Check the result, not just the green status

My backup job said `OK` while PBS had no snapshots. The job had written to local storage. Checking the destination and restoring into VM `999` caught the difference between a completed job and a backup I could use.

The same thing happened with Group Policy: the computer showed the GPO, but the user settings weren't applying. `gpresult /r` made that visible. Loopback processing in Merge mode got the drive map and screensaver settings onto `PC01`.

## Test access before changing it

I checked SSH key login in a second session before disabling password login. On Ubuntu I also learned to use `systemctl restart ssh`; trying `sshd` gave me a service-not-found error.

## Do it by hand, then automate it

Installing node_exporter three times made the Ansible use case pretty obvious. The second playbook run reported no changes on all three nodes. The original Docker playbook used a raw Compose command that reported a change even when there was nothing to recreate. I replaced it with the Compose module and corrected the APT architecture setting; those changes still need a new run against the lab.

## Leave notes I can use again

The commands, screenshots, and mistakes are the parts I'll want when I rebuild something. A list of tools doesn't tell me why the Docker install failed or which storage target I picked by mistake, so I kept those details here.
