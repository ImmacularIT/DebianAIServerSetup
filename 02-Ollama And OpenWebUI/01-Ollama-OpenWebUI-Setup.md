# Ollama & OpenWebUI Docker Setup

## Ollama with Nvidia GPU

Ollama makes it easy to get up and running with large language models locally.
To run Ollama using an Nvidia GPU, follow these steps:

### Step 1: Install the NVIDIA Container Toolkit

#### Install with Apt

Install the GnuPG:

```bash
sudo apt install gnupg
sudo apt clean
```

1. **Configure the repository**:

    ```bash
    curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey \
     | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg
    ```
    ```bash
    curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list \
     | sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' \
     | sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
    ```
    ```bash
    sudo apt-get update
    ```

2. **Install the NVIDIA Container Toolkit packages**:

    ```bash
    sudo apt-get install -y nvidia-container-toolkit
    sudo apt clean
    ```

### Step 2: Configure Docker to Use Nvidia Driver

```bash
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```

### Step 3: Start the Container

```bash
docker run -d --gpus=all -v ollama:/root/.ollama -p 11434:11434 --restart always --name ollama ollama/ollama
```

Ollama will get into sleep mode after a short while and it will take a short amount of time to wake it up again. This will have the effect that it takes a while for the Ollama to respond again. To make Ollama not go into sleep during next 5 hours (for example), use this command line when starting it up:

```bash
docker run -d --gpus=all -v ollama:/root/.ollama -p 11434:11434 -e OLLAMA_KEEP_ALIVE=5h --restart always --name ollama ollama/ollama
```

To have it permanently alive (GPU memory never unloaded):

```bash
docker run -d --gpus=all -v ollama:/root/.ollama -p 11434:11434 -e OLLAMA_KEEP_ALIVE=-1 --restart always --name ollama ollama/ollama
```

Check the current setting:

```bash
docker exec ollama printenv OLLAMA_KEEP_ALIVE
```

## Running Multiple Instances with Specific GPUs

You can run multiple instances of the Ollama server and assign specific GPUs to each instance. For example, using 4 Nvidia 3090 GPUs follow the below:

### Ollama Server for GPUs 0 and 1

```bash
docker run -d --gpus '"device=0,1"' -v ollama:/root/.ollama -p 11435:11434 --restart always --name ollama1 --network ollama-network ollama/ollama
```

### Ollama Server for GPUs 2 and 3

```bash
docker run -d --gpus '"device=2,3"' -v ollama:/root/.ollama -p 11436:11434 --restart always --name ollama2 --network ollama-network ollama/ollama
```

## Running Models Locally

Once the container is up and running, you can execute models using:

```bash
docker exec -it ollama ollama run llama3.1
```

```bash
docker exec -it ollama ollama run llama3.1:70b
```

```bash
docker exec -it ollama ollama run qwen2.5-coder:1.5b
```

```bash
docker exec -it ollama ollama run deepseek-v2
```

### Try Different Models

Explore more models available in the [Ollama library](https://github.com/ollama/ollama).

### Remove a Model

```bash
docker exec -it ollama ollama rm [name]
```

## OpenWebUI Installation

To install and run OpenWebUI, use the following command:

```bash
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

### Access OpenWebUI

Once installed, OpenWebUI runs on port 3000. You can access it by opening a browser and navigating to:

```
http://<your-server-ip>:3000
```
Note: Use "http", not "https".
