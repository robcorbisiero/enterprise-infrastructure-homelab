# Organizational Units and Group Policy

## What I was doing

I used the Windows 10 client I imaged through WDS to test OUs and Group Policy in `lab.corbitpros.com`. I wanted a mapped drive and a screensaver lock to apply on `PC01`.

## Client Provisioning

A Windows 10 image already staged in WDS was PXE-booted onto a spare mini PC. The unattended install joined the machine to the domain automatically, landing the computer object in the default `Computers` container.

## OU Design

```text
lab.corbitpros.com
├── Workstations
└── Servers
```

- `Workstations` holds domain-joined client machines (e.g. `PC01`), so workstation-scoped settings (drive mapping and screensaver lock) have a clean link target.
- `Servers` is created for future member servers. Domain Controllers are intentionally left in their default `Domain Controllers` OU rather than moved.

The default `Computers` container is not an OU, so anything left there cannot receive OU-linked Group Policy — this is why new computer objects need to be relocated after joining.

## PowerShell Commands Used

Run on the domain controller, elevated, with the Active Directory module loaded:

```powershell
New-ADOrganizationalUnit -Name "Workstations" -Path "DC=lab,DC=corbitpros,DC=com"
New-ADOrganizationalUnit -Name "Servers" -Path "DC=lab,DC=corbitpros,DC=com"
```

Find the newly domain-joined client sitting in the default container:

```powershell
Get-ADComputer -Filter *
```

Move it into `Workstations`:

```powershell
Move-ADObject -Identity "CN=PC01,CN=Computers,DC=lab,DC=corbitpros,DC=com" `
  -TargetPath "OU=Workstations,DC=lab,DC=corbitpros,DC=com"
```

Verify the move:

```powershell
Get-ADComputer -Identity "PC01" | Select-Object Name, DistinguishedName
```

## Screenshots

![Active Directory Users and Computers OU structure](../screenshots/windows-server/ad-ou-structure.png)

`Workstations` and `Servers` OUs created at the domain root, with `PC01` (the WDS-imaged Windows 10 client) relocated into `Workstations`.

## Domain-Wide Account Policies

I configured the default domain password and lockout settings in **Default Domain Policy**, linked at the domain root. The OU-linked policy below handles the workstation settings. The values here record my lab exercise.

Password Policy:

| Setting | Value |
| --- | --- |
| Enforce password history | 24 passwords remembered |
| Maximum password age | 42 days |
| Minimum password age | 1 day |
| Minimum password length | 12 characters |
| Password must meet complexity requirements | Enabled |
| Store passwords using reversible encryption | Disabled |

![Default Domain Policy password policy](../screenshots/windows-server/gpo-default-domain-password-policy.png)

Account Lockout Policy:

| Setting | Value |
| --- | --- |
| Account lockout duration | 15 minutes |
| Account lockout threshold | 5 invalid logon attempts |
| Allow Administrator account lockout | Enabled |
| Reset account lockout counter after | 10 minutes |

![Default Domain Policy account lockout policy](../screenshots/windows-server/gpo-default-domain-lockout-policy.png)

## Workstations Policy GPO

A separate GPO, `Workstations Policy`, was created and linked to the `Workstations` OU for settings that are correctly scoped at the OU level (unlike Account Policies above).

![Workstations Policy linked to the Workstations OU](../screenshots/windows-server/gpo-workstations-linked.png)

### Drive Mapping

Before the drive map, a file share was created on the domain controller:

```powershell
New-Item -Path "C:\LabShare" -ItemType Directory
New-SmbShare -Name "LabShare" -Path "C:\LabShare" -FullAccess "Everyone"
icacls "C:\LabShare" /grant "Authenticated Users:(OI)(CI)M"
```

For this lab share I used `Everyone: Full Control` at the share layer and `Authenticated Users: Modify` at the NTFS layer. That let me test the mapping; access was broad across authenticated users. For a real departmental share I would scope permissions to the relevant groups.

`Workstations Policy` → `User Configuration → Preferences → Windows Settings → Drive Maps` maps `Z:` to `\\LAB-DC-01\LabShare`:

![Drive map configuration in Workstations Policy](../screenshots/windows-server/gpo-drive-map-config.png)

### Hardening: Screensaver Lock

`Workstations Policy` → `User Configuration → Policies → Administrative Templates → Control Panel → Personalization`:

| Setting | Value |
| --- | --- |
| Enable screen saver | Enabled |
| Password protect the screen saver | Enabled |
| Screen saver timeout | Enabled |

Note this lives under **User Configuration**, not Computer Configuration — screensaver behavior is a per-user desktop setting, so it isn't found under Computer Configuration's Personalization node.

![Screensaver hardening settings in Workstations Policy](../screenshots/windows-server/gpo-personalization-screensaver.png)

## Troubleshooting: User Settings Not Applying

Initial validation with `gpresult /r` showed `Workstations Policy` applied under **COMPUTER SETTINGS**, but **USER SETTINGS** showed no GPOs applied at all (`N/A`) — meaning the drive map and screensaver policy, both `User Configuration` settings, were never reaching the logged-in user.

**Cause:** `User Configuration` settings apply based on which OU the *user object* lives in, not which OU the computer lives in. `LAB\Administrator`'s account lives in the default `Users` container (`CN=Administrator,CN=Users,DC=lab,DC=corbitpros,DC=com`), not in `Workstations` — so even though `PC01` (the computer) is correctly placed in `Workstations`, the GPO's user-side settings were never evaluated for a user object outside that OU.

**Fix:** Enabled Group Policy Loopback Processing on `Workstations Policy` so user-side settings are applied based on the *computer's* OU instead of the user's:

- `Workstations Policy` → `Computer Configuration → Policies → Administrative Templates → System → Group Policy` → **"Configure user Group Policy loopback processing mode"** → Enabled, mode **Merge** (keeps the user's own GPOs in addition to `Workstations Policy`, rather than replacing them).

After `gpupdate /force` and a fresh `gpresult /r`, `Workstations Policy` appeared in both the Computer Settings and User Settings applied lists:

```text
COMPUTER SETTINGS
Applied Group Policy Objects
-----------------------------
    Workstations Policy
    Default Domain Policy

USER SETTINGS
Applied Group Policy Objects
-----------------------------
    Workstations Policy
```

## Validation

The `Z:` drive mapping appeared correctly under Network locations on `PC01`:

![Mapped LabShare drive validated on PC01](../screenshots/windows-server/pc01-mapped-drive-validation.png)

The screensaver/lock policy was also confirmed: after the configured timeout with no input, `PC01` triggered the screensaver and required the account password to unlock, matching the `Password protect the screen saver` setting.

## How it turned out

After enabling loopback processing, I checked that `Workstations Policy` appeared for both the computer and user. The mapped drive showed up on `PC01`, and the screensaver required a password to unlock.
