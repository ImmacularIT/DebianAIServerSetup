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

Select "NVIDIA Proprietary"
Then press "OK" on the appearing warnings till installation continues.
At the end, choose "No" when asked for setting up X configuration.
Then "OK".

### Step 5: Update the initramfs and reboot

```bash
update-initramfs -u
reboot
```

### Step 6: Check if the Nvidia card is active

Check if the Kernel is using Nvidia:

```bash
lspci -s 13:00.0 -v | grep Kernel
```

Should get something like this if everything is correct:

```bash
Kernel driver in use: nvidia
Kernel modules: nouveau, nvidia_drm, nvidia
```

If the "Kernel driver in use:" is displaying other than "nvidia", the driver isn't correctly installed.

Display the GPU status with "nvtop":

```bash
nvtop
```

### Step 7: Install GPU Power Limit (optional)

This example set maximum to 170W.
Open a terminal and create a new systemd service file:

```bash
nano /etc/systemd/system/nvidia-power-limit.service
```

Add the text (uncomment if more GPUs are used):

```bash
[Unit]
Description=Set NVIDIA GPU Power Limit

[Service]
Type=oneshot
ExecStart=/usr/bin/nvidia-smi -i 0 -pl 170
#ExecStart=/usr/bin/nvidia-smi -i 1 -pl 170
#ExecStart=/usr/bin/nvidia-smi -i 2 -pl 170
#ExecStart=/usr/bin/nvidia-smi -i 3 -pl 170

[Install]
WantedBy=multi-user.target
```
(save/exit)

Reload the systemd manager configuration:

```bash
systemctl daemon-reload
```

Enable the service to ensure it runs at startup:

```bash
systemctl enable nvidia-power-limit.service
```

(Optional) Start the Service Immediately:

```bash
systemctl start nvidia-power-limit.service
```

Check the power limits using nvidia-smi:

```bash
nvidia-smi -q -d POWER
```

Look for the "Power Management" section to verify the new power limits.

### Step 8: Add the CUDA Repository

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/debian12/x86_64/cuda-keyring_1.1-1_all.deb
dpkg -i cuda-keyring_1.1-1_all.deb
apt update
apt -y install cuda
apt clean
```

Configure Environment Variables
Create the Cuda environment file:

```bash
# nano /etc/profile.d/cuda.sh
```

And put the following content inside:

```bash
export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
```
(save/exit)

Make the file executable:

```bash
chmod +x /etc/profile.d/cuda.sh
source /etc/profile.d/cuda.sh
```

Verify Installation:

```bash
nvcc --version
nvidia-smi
```

This should display details on the CUDA compile driver, including the installed version.

### Step 9: Setting Up cuDNN with CUDA (optional)

Visit the NVIDIA cuDNN Archive with a browser.
Since this need an free account, the only option to download via a browser and then transfer the file to the server via FileZilla or something.

Navigate to the NVIDIA cuDNN Archive:
https://developer.nvidia.com/rdp/cudnn-archive

Select "Local Installer for Linux x86_64 (Tar)". Then you have Register/Login, which is free.
(https://developer.nvidia.com/downloads/compute/cudnn/secure/8.9.7/local_installers/12.x/cudnn-linux-x86_64-8.9.7.29_cuda12-archive.tar.xz)

Then transfer the downloaded file to the server.

```bash
tar -xvf cudnn-linux-x86_64-8.9.7.29_cuda12-archive.tar.xz
cd cudnn-linux-x86_64-8.9.7.29_cuda12-archive
```
Then copy some library files to the Cuda directory.

```bash
cp include/cudnn*.h /usr/local/cuda/include/
cp lib/libcudnn* /usr/local/cuda/lib64/
chmod a+r /usr/local/cuda/include/cudnn*.h /usr/local/cuda/lib64/libcudnn*
```

Then delete the source directory (and library file):

```bash
cd ..
rm -R cudnn-linux-x86_64-8.9.7.29_cuda12-archive
rm cudnn-linux-x86_64-8.9.7.29_cuda12-archive.tar.xz
```

(skip below)
Set Up Environment Variables.
This has to done for each and every user that will use the cuDNN libraries.

```bash
nano ~/.bashrc
```

And put the following content inside:

```bash
export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
```
(save/exit)

```bash
source ~/.bashrc
```

Repeat the file and source for each user by logging in to their accounts and perform the same:

```bash
login (user)
```

Then go back to the root login.
