# Docker Installation on Ubuntu 2024.04 LTS (HX370)

In China, the connection to Docker Hub can be unstable. This guide focuses solely on installing Docker on the following environment and does not cover workarounds for connectivity issues.

**Hardware**: HX370  
**Software**: Ubuntu 2024.04 LTS

## Set Up Docker's APT Repository

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

## Install Docker Packages

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## Verify Installation

Verify that the installation is successful by running the `hello-world` image:

```bash
sudo docker run hello-world
```

## Enable Non-Root User to Run Docker

To allow a user to run Docker commands without `sudo`, add them to the Docker group:

```bash
sudo usermod -aG docker USERNAME
```

After logging out and back in, verify by listing running containers:

```bash
docker ps
```

This should display any existing containers if you have pulled images previously.
