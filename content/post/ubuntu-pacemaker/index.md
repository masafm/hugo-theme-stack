---
title: "Ubuntu 20.04でpacemakerを使う"
description: 
date: 2020-10-31T10:17:25+09:00
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
    - pacemaker
---
インストール
```
apt install pcs
```
haclusterユーザのパスワード設定
```
passwd hacluster
```
インストール後はデフォルトでnode1というホストでクラスタが組まれておりこいつが邪魔をして設定ができないので消す
```
pcs cluster destroy
```
クラスタを組む
```
pcs host auth <host1> <host2> <host3> -u hacluster
pcs cluster setup <cluster_name> <host1> <host2> <host3> --start --enable
```
STONITHを無効にする
```
pcs property set stonith-enabled=false
```
クラスタにVirtual IPを追加する
```
pcs resource create VirtualIP ocf:heartbeat:IPaddr2 ip=192.168.151.254 cidr_netmask=22 nic=br0 op monitor interval=10s
```
ステータス確認
```
root@mks-m75q-1:~# pcs status
Cluster name: mks-m75q
Cluster Summary:
  * Stack: corosync
  * Current DC: mks-m75q-3 (version 2.0.3-4b1f869f0f) - partition with quorum
  * Last updated: Sat Oct 31 20:58:20 2020
  * Last change:  Sat Oct 31 20:30:47 2020 by root via cibadmin on mks-m75q-1
  * 3 nodes configured
  * 1 resource instance configured

Node List:
  * Online: [ mks-m75q-1 mks-m75q-2 mks-m75q-3 ]

Full List of Resources:
  * VirtualIP   (ocf::heartbeat:IPaddr2):        Started mks-m75q-1

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```
VMのHA
```
pcs resource create vm-name ocf:heartbeat:VirtualDomain config=/etc/libvirt/qemu/name.xml migration_transport=ssh meta allow-migrate=true
```

https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/6/html/configuring_the_red_hat_high_availability_add-on_with_pacemaker/remotenode_config
