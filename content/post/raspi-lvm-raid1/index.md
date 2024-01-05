---
title: "Raspberry Pi でLVM raid1をルートパーティションにする"
description: 
date: 2020-10-25T10:18:25+09:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - Linux
tags:
    - Raspberry Pi
    - LVM
    - RAID
---
```
echo raid1 >>/etc/initramfs-tools/modules
update-initramfs -c -k $(uname -r)
echo "initramfs initrd.img-$(uname -r) followkernel" >>/boot/config.txt
# vi /boot/cmdline.txt
root=/dev/mapper/system-rootfs
```
