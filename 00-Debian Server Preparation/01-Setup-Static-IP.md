# Setup Static IP on Debian 13

This guide walks you through on how to setup Static IP on a Debian headless server.

---

## Step 1: Find your network interface name

Run:

```bash
ip link
```

## Step 2: Edit the interfaces file

Open the main network config:

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

Replace:

* ens3 → your interface name
* 192.168.1.50 → the static IP you want
* 192.168.1.1 → your network’s gateway
* DNS servers can be whatever you prefer (1.1.1.1 8.8.8.8 are Cloudflare/Google)

## Step 3: Apply the configuration

Restart networking:

```bash
systemctl restart networking
```

## Step 4: Verify everything

Check your IP:

```bash
ip addr show
```

Check your routes:

```bash
ip route
```

Test connectivity:

```bash
ping -c 3 8.8.8.8
ping -c 3 google.com
```
