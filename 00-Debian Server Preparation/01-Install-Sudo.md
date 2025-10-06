# Installing Sudo on Debian 13

This guide walks you through installing the Sudo command on a Debian headless server.

---

Login as root and enter following:

```bash
apt update
apt upgrade -y
apt install sudo
apt clean
apt autoremove
```

Enter the name of the user (username) that will be given the Sudo rights:

```bash
usermod -aG sudo (username)
```

Then Reboot

```bash
reboot
```
