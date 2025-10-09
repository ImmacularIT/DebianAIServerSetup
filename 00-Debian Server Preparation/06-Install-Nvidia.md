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
apt install linux-headers-$(uname -r) build-essential software-properties-common make nvtop htop gcc cmake -y
apt clean
```

### Step 4: Update the initramfs and reboot

```bash
update-initramfs -u
reboot
```


apt install linux-headers-$(uname -r) build-essential software-properties-common make nvtop htop gcc cmake -y

