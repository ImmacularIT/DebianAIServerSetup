# Remove the IPv6 handling on Debian 13

This guide walks you through how to disable the IPv6 network on a Debian headless server.

---

Login as root and enter following:

```bash
nano /etc/default/grub
```

Locate the lines beginning with GRUB_CMDLINE_LINUX_DEFAULT and GRUB_CMDLINE_LINUX. They might look something like:

```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet"
GRUB_CMDLINE_LINUX=""
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
