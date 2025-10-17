# Setup Static IP on Debian 13

This guide walks you through on how to setup Static IP on a Debian headless server.

---

##Step 1: Find your network interface name

```bash
ip link
```


Login as root and enter following:

```bash
nano /etc/network/interfaces
```

Then modify or add the following configuration - replacing the values with your actual details:

```bash
source /etc/network/interfaces.d/*

auto lo
iface lo inet loopback

auto ens3
iface ens3 inet static
    address 192.168.1.50
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 1.1.1.1 8.8.8.8
```

Add ipv6.disable=1 inside those quotes. For example:

```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet ipv6.disable=1"
GRUB_CMDLINE_LINUX="ipv6.disable=1"
```

Update Grub:

```bash
update-grub
```

Then Reboot

```bash
reboot
```
