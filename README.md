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

## Rancher Host - 10.10.13.2

1. Download latest Rancher ISO for Proxmox [here](https://rancher.com/docs/os/v1.x/en/installation/workstation/boot-from-iso/)
2. Upload the RancherOS iso to (local)pve
3. Setup a VM with RancherOS ISO as CD. Give it at least 3gb ram to start
4. Boot
5. Open console and change default rancher password `sudo passwd rancher`
6. Find ip address `ifconfig`
7. SSH in to Rancher host rancher@IP_ADDRESS
8. Create a `cloud-config.yml` - `vi cloud-config.yml`
9. Paste in the following config
```
#cloud-config

hostname: <HOSTNAME>

rancher:
  network:
    interfaces:
      eth0:
        address: 10.10.13.2/24
        gateway: 10.10.13.254
        mtu: 1500
        dhcp: false
    dns:
      nameservers:
      - 1.1.1.1
      - 8.8.4.4

ssh_authorized_keys:
  - ssh-rsa <OUTPUT OF pbcopy ~/.ssh/id_rsa.pub>
```
10. Press `ESC` then type `:wq` then press `Enter` to exit 
11. Validate the `cloud-config.yml` - `sudo ros config validate -i cloud-config.yml`
12. Install the `cloud-config.yml` - `sudo ros install -c cloud-config.yml -d /dev/sda`
13. Remove CD Image from VM, and then reboot
14. SSH back in to the Rancher Host

## Create NFS Shares on FreeNAS 10.10.13.5

1. Create a UNIX dataset called `/mnt/array/exports/rancher` with root and wheel as the user, set a quota for 50gb
2. Create a NFS share to the dataset you created, select `all dirs`, click on `Advanced Mode`, Mapall User `root` and Mapall Group `wheel`
3. Enable nfs sharing and click edit and select the following
  - Allow non-root mount
  - Enable NFSv4
  - NFSv3 ownership model for NFSv4
4. Reboot FreeNAS

## Mount NFS Share on RancherOS

`sudo ros config set mounts '[["10.10.13.5:/mnt/array/exports/rancher", "/mnt/rancher", "nfs4",""]]'`
