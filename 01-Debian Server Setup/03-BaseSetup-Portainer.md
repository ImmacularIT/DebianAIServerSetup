# Installing Portainer on Debian 13

This guide walks you through installing the Portainer on a Debian headless server.

---

Login as root

### docker volume create portainer_data

```bash
docker run -d -p 8000:8000 -p 9443:9443 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```

### Access Portainer on:

`https://<your-server-ip>:9443`
