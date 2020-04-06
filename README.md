# Homelab

## Proxmox Host - 10.10.13.1

1. Download latest Proxmox ISO [here](https://www.proxmox.com/en/proxmox-ve/get-started)
2. Flash a USB Stick with the ISO - [Etcher](https://www.balena.io/etcher/) is one way to do that
3. [Install](https://pve.proxmox.com/wiki/Installation) Proxmox on the host
4. `nano  /etc/apt/sources.list` edit to 
```
deb http://ftp.debian.org/debian buster main contrib
deb http://ftp.debian.org/debian buster-updates main contrib

# PVE pve-no-subscription repository provided by proxmox.com,
# NOT recommended for production use
deb http://download.proxmox.com/debian/pve buster pve-no-subscription

# security updates
deb http://security.debian.org buster/updates main contrib
```

5. `nano /etc/apt/sources.list.d/pve-enterprise.list` edit to 
```
#deb https://enterprise.proxmox.com/debian/pve buster pve-enterprise
```
6. `apt update && apt dist-upgrade -y`
7. To edit the node name - follow this [guide](https://pve.proxmox.com/wiki/Renaming_a_PVE_node) 
8. Reboot host `reboot now`
