# Installing Docker on Debian 13

This guide walks you through installing the Docker cli base on a Debian headless server.

---

## Installing Docker on Debian

This section covers installing Docker on Debian.

### Step 1: Add Docker storage disk

It it recommended to add an extra storage disk over the Docker library since it is easy to run out space. If the disk space on the OS disk runs out, the OS will behave strange and finally halt with no chance to start again.
So let's propare the extra storage.

Prerequisites:

```bash
1 extra disk with 1 partition, /dev/sdb1, formatted with EXT4 (or other).
```

Since Docker isn't installed yet, we need to create the Docker directory:

```bash
sudo mkdir /var/lib/docker
```

Get the BLKID of the /dev/sdb1:

```bash
sudo blkid /dev/sdb1
```

Example of output:

```bash
/dev/sdb1: UUID="b85e7463-ba57-444e-992f-c94cd7a09f9d" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="4db7408d-01"
```



```bash
# /dev/sdb1 to be mounted on /var/lib/docker
UUID=4dc584bf-7e36-46d3-b4b8-94ca1b7b21a3 /var/lib/docker                ext4    defaults 0       2
```




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
