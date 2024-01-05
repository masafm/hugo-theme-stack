---
title: "nmcliでbridgeとbondingとvlanを組み合わせる"
description: 
date: 2020-10-25T10:16:25+09:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
---
nmtuiではできないっぽいのでnmcliでやる

# bridgeとbondingの組み合わせ
まずbridgeを作る
```
nmcli c add type bridge ifname br0 con-name br0
nmcli c mod br0 bridge.stp no
nmcli c mod br0 ipv4.method manual ipv4.address "192.168.1.100/24" ipv4.gateway "192.168.1.1" ipv4.dns "8.8.8.8 8.8.4.4" ipv4.dns-search lan
nmcli c down br0
nmcli c up br0
```
bridgeにbondingを追加
```
nmcli c add type bond ifname bond0 con-name bond0 mode active-backup
nmcli c mod bond0 connection.master br0 connection.slave-type bridge
nmcli c add type bond-slave ifname enpxxx con-name bond-slave-enpxxx master bond0
nmcli c add type bond-slave ifname enpyyy con-name bond-slave-enpyyy master bond0
nmcli c down bond-slave-enpxxx
nmcli c down bond-slave-enpyyy
nmcli c down bond0
nmcli c up bond-slave-enpxxx
nmcli c up bond-slave-enpyyy
nmcli c up bond0
```

# bridgeとbondingの組み合わせで更にVLANも指定する
まず上のbridgeとbonding組み合わせを作る

VLAN ID2の場合
```
nmcli c add type bridge ifname br0.2 con-name br0.2;\
nmcli c mod br0.2 bridge.stp no;\
nmcli c mod br0.2 ipv4.method disable;\
nmcli c mod br0.2 ipv6.method disable;\
nmcli c down br0.2;\
nmcli c up br0.2;\
nmcli c add type vlan ifname bond0.2 con-name bond0.2 dev bond0 id 2;\
nmcli c mod bond0.2 connection.master br0.2 connection.slave-type bridge;\
nmcli c down bond0.2;\
nmcli c up bond0.2;
```

VLAN ID3の場合
```
nmcli c add type bridge ifname br0.3 con-name br0.3;\
nmcli c mod br0.3 bridge.stp no;\
nmcli c mod br0.3 ipv4.method disable;\
nmcli c mod br0.3 ipv6.method disable;\
nmcli c down br0.3;\
nmcli c up br0.3;\
nmcli con add type vlan ifname bond0.3 con-name bond0.3 dev bond0 id 3;\
nmcli c mod bond0.3 connection.master br0.3 connection.slave-type bridge;\
nmcli c down bond0.3;\
nmcli c up bond0.3;
```
