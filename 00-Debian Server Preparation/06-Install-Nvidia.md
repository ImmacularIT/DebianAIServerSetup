# Installing Nvidia drivers on Debian 13

This guide walks you through installing Nvidia drivers for AI use on a Debian 13 headless server.

---

## Part 1: Detect Nvidia card and Blacklist Kernel nouveau

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

### Step 3: Install utilities

```bash
apt install linux-headers-$(uname -r) build-essential make nvtop htop gcc cmake -y
apt clean
```

### Step 4: Install latest Nvidia drivers

Get the latest Nvidia driver at:

```bash
https://www.nvidia.com/en-us/drivers/unix/
```

Click and surf to the download page.
At the time of writing, I selected:

```bash
Linux x86_64/AMD64/EM64T
Latest Production Branch Version: 580.95.05
```

Left click and copy the download url at the download page.
Then, in the Debian CLI, ennter the following:

```bash
wget https://us.download.nvidia.com/XFree86/Linux-x86_64/580.95.05/NVIDIA-Linux-x86_64-580.95.05.run
chmod +x NV*
./NV* --dkms
```

### Step 5: Update the initramfs and reboot

```bash
update-initramfs -u
reboot
```


apt install linux-headers-$(uname -r) build-essential software-properties-common make nvtop htop gcc cmake -y

