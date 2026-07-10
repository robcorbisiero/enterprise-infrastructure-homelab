# Organizational Units and Group Policy

## Objective

Introduce OU-based structure and Group Policy management for the `lab.corbitpros.com` domain, starting with a domain-joined Windows 10 client imaged through WDS.

## Client Provisioning

A Windows 10 image already staged in WDS was PXE-booted onto a spare mini PC. The unattended install joined the machine to the domain automatically, landing the computer object in the default `Computers` container.

## OU Design

```text
lab.corbitpros.com
├── Workstations
└── Servers
```

- `Workstations` holds domain-joined client machines (e.g. `PC01`), so workstation-scoped GPOs (password policy, drive mapping, hardening) have a clean link target.
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

## Evidence

![Active Directory Users and Computers OU structure](../screenshots/windows-server/ad-ou-structure.png)

`Workstations` and `Servers` OUs created at the domain root, with `PC01` (the WDS-imaged Windows 10 client) relocated into `Workstations`.

## Next Steps

- Create GPOs scoped to the `Workstations` OU: a password/account lockout policy, a Group Policy Preferences drive mapping to a Windows Server file share, and a hardening policy (e.g. screen lock timeout or a software restriction rule).
- Validate policy application on `PC01` with `gpupdate /force` and `gpresult /r`.
