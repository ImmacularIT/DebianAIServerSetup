# Installing Docker on Debian 13

This guide walks you through installing the Docker cli base on a Debian headless server.

---

## Installing Docker on Debian

This section covers installing Docker on Debian.

### Step 1: Remove Older Versions

If you have previous versions of Docker installed, remove them:

```bash
sudo apt remove docker docker-engine docker.io containerd runc
```

### Step 2: Add Docker's Official GPG Key and Repository

Add Docker’s GPG key and repository to your system’s Apt sources:

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```
```bash
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
```bash
sudo apt-get update
```

### Step 3: Install Docker

Now, install Docker:

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Step 4: Post-Installation Steps

To allow your user to run Docker commands without `sudo`, add your user to the Docker group:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Test your Docker installation by running the following command:

```bash
docker run hello-world
```

For more information, visit the official [Docker installation page](https://docs.docker.com/engine/install/debian/).
