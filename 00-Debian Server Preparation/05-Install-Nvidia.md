# Installing Nvidia drivers on Debian 13

This guide walks you through installing Nvidia drivers for AI use on a Debian 13 headless server.

---

## Part 1: Detect if Nvidia card is present and Blacklist Kernel nouveau driver

The Linux Kernel's built in Nvidia driver needs to be disabled before installing the Nvidia propriety driver.

### Step 1: Detect if Nvidia card is present

Lgoin as root.
Check if the Nvidia Graphics card is detected:

```bash
lspci | grep -i NVIDIA
```

Depending of the card's model, the result should be something like:

```bash
13:00.0 VGA compatible controller: NVIDIA Corporation GA106 [GeForce RTX 3060 Lite Hash Rate] (rev a1)
```

### Step 2: Blacklist Kernel nouveau driver for Nvidia

Create new file:

```bash
nano /etc/modprobe.d/blacklist-nouveau.conf
```

And put the following content inside:

```bash
blacklist nouveau
options nouveau modeset=0
```





Install "parted" in order to handle the disks.

```bash
sudo apt install parted -y
```

### Step 3: Add the Webmin Repository and Key

To add the Webmin repository, download and run the setup script.

```bash
curl -o setup-repos.sh https://raw.githubusercontent.com/webmin/webmin/master/setup-repos.sh
sudo sh setup-repos.sh
```

### Step 4: Install Webmin

With the repository set up, install Webmin:

```bash
sudo apt-get install webmin --install-recommends
```

### Step 5: Access Webmin

Once installed, Webmin runs on port 10000. You can access it by opening a browser and navigating to:

```
https://<your-server-ip>:10000
```

Then login as root.

If you are using a firewall, allow traffic on port 10000:

```bash
sudo ufw allow 10000
```

You can now log in to Webmin using your system's root credentials.

---

## Part 2: Expanding a Logical Volume Using Webmin

Expanding a logical volume through Webmin’s Logical Volume Management (LVM) interface is a simple process.

### Step 1: Access Logical Volume Management

Log in to Webmin and navigate to:

**Hardware > Logical Volume Management**

Here, you can manage physical volumes, volume groups, and logical volumes.

### Step 2: Add a New Physical Volume

If you've added a new disk or partition to your system, you need to allocate it to a volume group before expanding the logical volume. To do this:
1. Locate your volume group in the Logical Volume Management module.
2. Click **Add Physical Volume**.
3. Select the new partition or RAID device and click **Add to volume group**. This action increases the available space in the group.

### Step 3: Resize the Logical Volume

To extend a logical volume:
1. In the **Logical Volumes** section, locate the logical volume you wish to extend.
2. Select **Resize**.
3. Specify the additional space or use all available free space in the volume group.
4. Click **Apply** to resize the logical volume.

### Step 4: Resize the Filesystem

After resizing the logical volume, expand the filesystem to match:
1. Click on the logical volume to view its details.
2. For supported filesystems like ext2, ext3, or ext4, click **Resize Filesystem**. The filesystem will automatically adjust to the new size of the logical volume.
