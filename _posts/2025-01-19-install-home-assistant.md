---
layout: post
title: Install HomeAssistant (qcow2)
date: 2025-01-22 23:06 +1100
categories: [Home Assistant]
tags: [configuration, homeassistant, proxmox]
---

# Install Guide for qcow2 on proxmox
## Obtain the VM image
- Navigate to the installation page on the HA [website:](https://www.home-assistant.io/installation/alternative)
- Simply right-click the KVM/Proxmox link and copy the address
- In your Proxmox console, use wget to download the file
```bash
wget <ADDRESS>
```
- Expand the compressed image
```bash
unxz </path/to/file.qcow2.xz>
```

## Create the VM
### General:
- Select your VM name and ID
- Select 'start at boot'
### OS:
- Select 'Do not use any media'
### System:
- Change 'machine' to 'q35'
- Change BIOS to OVMF (UEFI)
- Select the EFI storage (typically local-lvm)
- Uncheck 'Pre-Enroll keys'
### Disks:
- Delete the SCSI drive and any other disks
### CPU:
- Set minimum 2 cores
### Memory:
- Set minimum 4096 MB
### Network:
- Leave default unless you have special requirements (static, VLAN, etc)

Confirm and finish. Do not start the VM yet.

## Add the image to the VM
- In your node's console, use the following command to import the image from the host to the VM
```bash
Bash:
qm importdisk <VM ID> </path/to/file.qcow2> <EFI location>
```
Example:
```bash
Bash:
qm importdisk 205 /home/user/haos_ova-12.0.qcow2 local-lvm
```

- Close the node's console and select your HA VM
- Go to the 'Hardware' tab
- Select the 'Unused Disk' and click the 'Edit' button
- Check the 'Discard' box if you're using an SSD then click 'Add'
- Select the 'Options' tab
- Select 'Boot Order' and hit 'Edit'
- Check the newly created drive (likely scsi0) and uncheck everything else

## Finish Up
- Start the VM
- Check the shell of the VM. If it booted up correctly, you should be greeted with the link to access the Web UI.
- Navigate to `<VM IP>:8123`

Done. Everything should be up and running now.
