# Windows Deployment Services

## Objective

Prepare Windows Deployment Services (WDS) so the lab can image multiple mini PCs from a centralized Windows Server instead of installing each machine manually.

## Reason for WDS

The lab includes multiple mini PCs. WDS turns repeated manual installs into a repeatable deployment workflow and demonstrates practical endpoint provisioning skills.

## Planned Workflow

1. Install the WDS role on Windows Server 2016.
2. Configure WDS for integrated Active Directory operation.
3. Create an image group, such as `Windows_10_Enterprise`.
4. Add boot images from Windows installation media.
5. Add install images when ISO media is available.
6. PXE boot mini PCs from the lab network.
7. Deploy Windows images over the network.

## Current Status

- WDS role planning was completed.
- The need for ISO/install media was identified.
- Image group naming was planned.
- Full deployment testing is pending available ISO media and PXE/client validation.

## Skills Demonstrated

- Enterprise imaging concepts
- PXE deployment planning
- Centralized endpoint provisioning
- Windows Server role planning
- Repeatable infrastructure documentation
