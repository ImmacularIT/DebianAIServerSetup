
# ComfyUI Docker Setup with GGUF Support and ComfyUI Manager

This guide provides detailed steps to build and run **ComfyUI** with **GGUF support** and **ComfyUI Manager** using Docker. The GGUF format is optimized for quantized models, and ComfyUI Manager is included for easy node management.

## Prerequisites

Before starting, ensure you have the following installed on your system:

- **Docker**
- **NVIDIA GPU with CUDA support** (if using GPU acceleration)
- **Create Directory structure for git repo Models and Checkpoints**

## Prepare the extra disk space for model storage

The extra disk partition "/dev/sdb1" where earlier mounted on "/var/lib/docker" for storing all the Docker imagaes. This partition can also be used to store the ComfyUI models, that can be easially downloaded from the user's cli and then shared into ComfyUI in real time like a shared space.

### 1. Adjust permissions on /var/lib/docker for all members in the Docker group

Allow members of the docker group to traverse (but not list) the directory:

```bash
sudo chgrp docker /var/lib/docker
sudo chmod 0710 /var/lib/docker
```

### 2. Create the shared directory

Create the new shared folder "exstore" and assign ownership + permissions:

```bash
sudo mkdir /var/lib/docker/exstore
sudo chown root:docker /var/lib/docker/exstore
sudo chmod 2770 /var/lib/docker/exstore
```

### 3. Create user symlinks

Create a symlink in each docker-group user’s home directory pointing to the shared folder:

```bash
for user in $(getent group docker | awk -F: '{print $4}' | tr ',' ' '); do
  sudo -u "$user" ln -sfn /var/lib/docker/exstore "/home/$user/shared"
done
```

### 4. Verify

Check permissions:

```bash
ls -ld /var/lib/docker /var/lib/docker/exstore
```

Should show:

```bash
drwx--x--- 13 root docker 4096 Oct 12 07:37 /var/lib/docker
drwxrws---  2 root docker 4096 Oct 12 11:04 /var/lib/docker/exstore
```

### 5. Keep permissions persistant

Unfortunately, Docker will revert the permissions back to "root:root" on the "(/var/lib)/docker" directory everytime it starts. A fix is needed to change the directory back to the desired permissions when this happens.

Create the following servicefile:

```bash
sudo nano /etc/systemd/system/docker-fix-perms.service
```bash

Populate the servicefile with the following content:

```bash
[Unit]
Description=Reapply permissions for /var/lib/docker shared directory
After=docker.service
Requires=docker.service
RequiresMountsFor=/var/lib/docker

[Service]
Type=oneshot
ExecStart=/bin/chgrp docker /var/lib/docker
ExecStart=/bin/chmod 0710 /var/lib/docker
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

Reload and enable the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable docker-fix-perms.service
```

Create the following directory structure:

```bash
mkdir -p $HOME/shared/source
mkdir -p $HOME/shared/application/comfyui/models/checkpoints
mkdir -p $HOME/shared/application/comfyui/output
mkdir -p $HOME/shared/application/comfyui/workflow
```

## Create ComfyUI

### 1. Clone the ComfyUI Repository

First, navigate to source directory:

```bash
cd $HOME/shared/source
```

 Clone the ComfyUI repository to your local machine

```bash
git clone https://github.com/comfyanonymous/ComfyUI.git
cd ComfyUI
```

### 2. Create the Dockerfile

Copy the provided `Dockerfile` in the root of your ComfyUI directory. This file contains the necessary configurations for building the Docker container with GGUF support.
If you prefer to copy the content into an inplace file instead:

```bash
nano dockerfile
```

Copy the file's content and then paste it into the text window. Then Save and Exit.

### 3. Build the Docker Image

```bash
docker build -t comfyui-gguf:latest .
```

This will create a Docker image named `comfyui-gguf:latest` with both **ComfyUI Manager** and **GGUF support** built in.

### 4. Run the Docker Container

Once the image is built, you can run the Docker container with volume mapping for your models.

```bash
docker run --name comfyui -p 8188:8188 --gpus all \
  -v $HOME/shared/application/comfyui/models:/app/models \
  -v $HOME/shared/application/comfyui/output:/app/output \
  -v $HOME/shared/application/comfyui/workflow:/app/user \
  -d comfyui-gguf:latest
```

This command maps your local `models` and `output` directory to `/app/models` and `/app/output` inside the container and exposes ComfyUI on port `8188`.

### 5. Download and Place Checkpoint Models

Download and place your civitai checkpoint models in the `checkpoints` directory inside the container:
https://civitai.com/models/139562/realvisxl-v50

1. **Navigate to the Checkpoints Directory**:
   ```bash
   cd $HOME/shared/application/comfyui/models/checkpoints
   ```

2. **Download `realvisxlV50_v50LightningBakedvae.safetensors`**:
   ```bash
   wget "https://civitai.com/api/download/models/798204?type=Model&format=SafeTensor&size=full&fp=fp16" -O realvisxlV50_v50LightningBakedvae.safetensors
   ```

To use GGUF models or other safetensor models, follow the steps below to download them directly into the `checkpoints` directory.

1. **Navigate to the Checkpoints Directory**:
   ```bash
   cd $HOME/shared/application/comfyui/models/checkpoints
   ```

2. **Download `flux1-schnell-fp8.safetensors`**:
   ```bash
   wget "https://huggingface.co/Comfy-Org/stable-diffusion-v1-5-archive/resolve/main/v1-5-pruned-emaonly-fp16.safetensors?download=true" -O v1-5-pruned-emaonly-fp16.safetensors
   ```

3. **Download `flux1-schnell-fp8.safetensors`**:
   ```bash
   wget "https://huggingface.co/Comfy-Org/flux1-schnell/resolve/main/flux1-schnell-fp8.safetensors?download=true" -O flux1-schnell-fp8.safetensors
   ```

4. **Download `flux1-dev-fp8.safetensors`**:
   ```bash
   wget "https://huggingface.co/Comfy-Org/flux1-dev/resolve/main/flux1-dev-fp8.safetensors?download=true" -O flux1-dev-fp8.safetensors
   ```

These commands will place the corresponding `.safetensors` files into the `checkpoints` directory.

### 6. Access ComfyUI

After starting the container, access the ComfyUI interface in your web browser:

```bash
http://<your-server-ip>:8188
```

Replace `<your-server-ip>` with your server's IP address or use `localhost` if you're running it locally.

### 7. Using GGUF Models

In the ComfyUI interface:
- Use the **UnetLoaderGGUF** node (found in the `bootleg` category) to load GGUF models.
- Ensure your GGUF files are correctly named and placed in the `/app/models/checkpoints` directory for detection by the loader node.

### 8. Managing Nodes with ComfyUI Manager

With **ComfyUI Manager** built into the image:
- **Install** missing nodes as needed when uploading workflows.
- **Enable/Disable** conflicting nodes from the ComfyUI Manager interface.

### 9. Stopping and Restarting the Docker Container

To stop the running container:

```bash
docker stop comfyui
```

To restart the container:

```bash
docker start comfyui
```

### 10. Logs and Troubleshooting

To view the container logs:

```bash
docker logs comfyui
```

This will provide details if anything goes wrong or if you encounter issues with GGUF models or node management.
