# Installing Sudo on Debian 13

This guide walks you through installing the Portainer on a Debian headless server.

---

Login as root

### docker volume create portainer_data

```bash
apt update
apt upgrade -y
apt install sudo
apt clean
apt autoremove
```

Enter the name of the user (username) that will be using Sudo rights:

```bash
usermod -aG sudo (username)
```

Then Reboot

```bash
reboot
```
