---
title: "USB HDDでUASを無効化"
description: 
date: 2020-10-25T10:17:25+09:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - Linux
tags:
    - usb
    - hdd
    - uas
---
ラズパイ4でUSB HDDがうまく動かなかったときのワークアラウンド
```
echo "usb-storage.quirks=0x0080:0x0578:u" >>/boot/cmdline.txt
```
