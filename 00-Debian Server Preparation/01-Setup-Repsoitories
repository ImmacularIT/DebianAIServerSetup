# Add the required Repositories for Debian 13

In order to install Nvidia drivers etc from the Repository, the proper Sources path needs to be entered.

## Part 1: Edit Source file:

Login as root.
Open the APT Sources file:

```bash
nano /etc/apt/sources.list
```

Add "contrib and non-free non-free-firmware" to the lines below. Should look like this:

```bash
deb http://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware
deb-src http://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware

deb http://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
deb-src http://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware

# bookworm-updates, to get updates before a point release is made;
# see https://www.debian.org/doc/manuals/debian-reference/ch02.en.html#_updates_and_backports
deb http://deb.debian.org/debian/ bookworm-updates main contrib non-free non-free-firmware
deb-src http://deb.debian.org/debian/ bookworm-updates main contrib non-free non-free-firmware
```

## Part 2: Refresh and update

Refresh the content and update + reboot if updates are found:

```bash
apt update
apt upgrade -y
apt autoremove
reboot
```
