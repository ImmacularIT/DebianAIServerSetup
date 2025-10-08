# Installing Wget and Curl on Debian 13

This guide walks you through installing the Wget and Curl commands on a Debian headless server.

---

Login as root and enter following:

```bash
apt update
apt upgrade -y
apt install curl wget
apt clean
apt autoremove
```
