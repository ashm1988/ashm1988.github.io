---
layout: post
title: Linux Cheat sheet
date: 2022-10-22 09:29 +1100
categories: [Cheat Sheets, Linux]
tags: [cheatsheet, linux, documentation]
---

# Commands 
## Mount 
### Mount drives
- Install cifs-utils to help manage mounts
```shell
sudo apt install cifs-utils
```
- Create the share on the server
```shell
sudo mkdir /mnt/<sharename>
```
- Temporally mount drive
```shell
sudo mount.cifs //<host>/<share-location> /mnt/<mountname>/ -o user=<share-username>,pass=<share-password>
```
- Check mounted drives
```shell
mount
```

