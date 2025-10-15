# Add the missing SBIN Path on Debian 13

This guide walks you through setting up the missing /usr/sbin Path on a Debian headless server.

---

Login as root and enter following:

```bash
# nano /etc/profile
```

At the top of the file, there is the following content:

```bash
# /etc/profile: system-wide .profile file for the Bourne shell (sh(1))
# and Bourne compatible shells (bash(1), ksh(1), ash(1), ...).

if [ "$(id -u)" -eq 0 ]; then
  PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
else
  PATH="/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games"
```

Add "/usr/sbin:" after "/usr/bin:" on the second row to make it look like this:

```bash
else
  PATH="/usr/local/bin:/usr/bin:/usr/sbin:/bin:/usr/local/games:/usr/games"
```

Then Reboot:

```bash
reboot
```
