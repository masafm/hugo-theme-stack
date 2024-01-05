---
title: "Ubuntu Server 22.04.3 LTS でxfce4と日本語環境の構築"
description: 
date: 2024-01-05T10:17:25+09:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - Linux
tags:
    - ubuntu
    - xfce4
---
xfce4､Ubuntu Desktop､日本語パックのインストール
```
apt install xfce4 xfce4-goodies ubuntu-desktop fonts-noto-cjk language-pack-ja
```
ロケールを日本語に変更､またGUIの言語サポートからGUI用の日本語パックも入れる
```
localectl set-locale LANG=ja_JP.UTF-8
```

xface4だとIBusに関する追加設定が必要([参考](https://wiki.archlinux.jp/index.php/IBus))
/etc/environment
```
GTK_IM_MODULE=ibus
QT_IM_MODULE=ibus
XMODIFIERS=@im=ibus
```

下記コマンドを自動起動するように設定
```
ibus-daemon -drxR
```
