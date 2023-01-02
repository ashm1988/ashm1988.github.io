---
layout: post
title: New Proxmox Server
date: 2022-10-22 11:24 +1100
categories: [HomeLab, Proxmox]
tags: [homelab, proxmox, vm]
---

Steps to take when configuring a new server on Proxmox 

> Install docker as part of the server install

If the server is a clone, just need to change the hostname

# Configuration
## General configuration
- update the server 
```bash
sudo apt-get update
sudo apt-get upgrade
```

- Install qemu-guest-agent
```bash
sudo apt install qemu-guest-agent
```

- Enable passwordless ssh from local machine
```bash
ssh-copy-id ash@<host-ip>
```

- Add server to ssh config

- Add user to sudo group if not already done
```bash
sudo usermod -aG sudo ash
```
- Add user to passwordless sudo
```bash
sudo visudo
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) NOPASSWD: ALL
```

## Install zsh
- Install zsh and make it the default shell
```bash
sudo apt install zsh
chsh -s $(which zsh)
```

## Install oh-my-zsh
- Install oh-my-zsh
```zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
- clone git repository with settings 
```zsh
git clone https://github.com/ashm1988/zshdotfiles.git
```
- copy the settings to home

## Change cloned server hostname
- Change hostname 
```bash
hostnamectl set-hostname <new-hostname>
```
- Update dns record in hosts file
```bash
sudo vim /etc/hosts
```
```bash
127.0.0.1 <server name>
```

# Troubleshooting 
- `apt update` Package issues 
(https://futurestud.io/tutorials/how-to-fix-ubuntu-debian-apt-get-404-not-found-repository-errors)
