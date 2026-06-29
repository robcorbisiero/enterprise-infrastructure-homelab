# Docker Host Setup

## Objective

Install Docker Engine and Docker Compose on the Ubuntu Server VM so the homelab can host containerized services.

## Intended Install Path

The target was the official Docker repository rather than the older packages bundled in Ubuntu's default repositories.

Planned packages:

```bash
docker-ce
docker-ce-cli
containerd.io
docker-compose-plugin
```

## Repository Setup

The modern Ubuntu approach uses a repository file under:

```text
/etc/apt/sources.list.d/docker.list
```

and a signed key under:

```text
/etc/apt/keyrings/
```

## Issue Encountered

`sudo apt update` returned:

```text
NO_PUBKEY 7EA0A9C3F273FCD8
The repository 'https://download.docker.com/linux/ubuntu noble InRelease' is not signed.
```

This meant APT could see the Docker repository, but could not verify it because the expected GPG key was missing, unreadable, or referenced from the wrong path.

## Troubleshooting Performed

- Found and corrected a typo from `sources.list.y` to `sources.list.d`.
- Recreated the Docker repository list file.
- Attempted to place Docker's key under `/etc/apt/keyrings/docker.gpg`.
- Identified that the key file was not present when `chmod` was attempted.
- Switched the plan toward using a readable `.asc` key path.

## Next Validation Steps

```bash
sudo mkdir -p /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

After install:

```bash
sudo systemctl status docker
sudo usermod -aG docker $USER
docker run hello-world
```

## Current Status

Docker installation was in progress at the time of documentation. The important learning outcome was repository trust troubleshooting, which is a common Linux administration task.
