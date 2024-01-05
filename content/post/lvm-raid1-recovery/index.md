---
title: "LVM raid1の復旧"
description: 
date: 2020-11-08T10:17:25+09:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - Linux
tags:
    - lvm
    - raid
---
```
vgreduce vgubuntu --removemissing --force
pvcreate /dev/nvme0n1p2
vgextend vgubuntu /dev/nvme0n1p2
lvconvert --repair /dev/vgubuntu/root
lvconvert --repair /dev/vgubuntu/swap_1
lvs -a -o name,copy_percent,devices vgubuntu
```
