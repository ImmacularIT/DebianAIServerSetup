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

Now, the FSTAB needs to be updated with the Mount point:

```bash
sudo nano /etc/fstab
```

At the end of the file, add the partion's UUID and the mount point as:

```bash
# /dev/sdb1 to be mounted on /var/lib/docker
UUID=b85e7463-ba57-444e-992f-c94cd7a09f9d /var/lib/docker                ext4    defaults 0       2
```
(save & exit)

Reload the Daemons:

```bash
sudo systemctl daemon-reload
```

Then mount all filesystems:

```bash
sudo mount -a
```

If everything is correct, there will be no error or warnings.
Check if it is mounted correctly:

```bash
sudo mount | grep docker
```

The output should be:

```bash
/dev/sdb1 on /var/lib/docker type ext4 (rw,relatime,errors=remount-ro)
```

### Step 2: Remove Older Docker versions

If you have previous versions of Docker installed, remove them:

```bash
sudo apt remove docker docker-engine docker.io containerd runc
```

### Step 3: Add Docker's Official GPG Key and Repository

Add Docker’s GPG:

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Add Docker’s repository to your system’s Apt sources:

```bash
sudo echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Then update:

```bash
sudo apt-get update
```

### Step 3: Install Docker

Install Docker:

```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
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
