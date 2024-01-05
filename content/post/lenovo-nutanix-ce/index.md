---
title: "Lenovo m75s gen2 smallにNutanix CE 2020.09.16を入れる"
description: 
date: 2021-03-28T10:17:25+09:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - Linux
tags:
    - lenovo
    - nutanix
---
インストール前に下記のスライドは必読
https://www.slideshare.net/smzksts/nutanix-community-edition-518

ドライブ構成はUSBメモリ(AHV), NVMe(CVM), SATA SSD(CVM), SATA HDD(DATA)
m75s gen2で躓いたポイントは以下

* USBからブート後、一度USBを抜き差ししないと`StandardError: Failed command: [hdparm -z /dev/sdc] with reason [BLKRRPART failed: Device or resource busy]`というメッセージが出てインストーラが起動しない
* 上記スライド p19 「インストール準備 起動優先度の設定 • に入り、 起動デバイスの優先順位を変更し、 インストーラー メモリが 最上位となるように設定する • キーや キー等 機種によって異なる で 入れるブートデバイス手動選択画面から 起動すると、なぜかスクリーンサイズ不足に なってしまい、インストールを続行できない （報告多数）」に従ってもインストールが続行できなかった。しょうがないのでUSB NIC(QNA-UC5G1T)をつないでsshdを起動し./ce_installer && screen -rでインストールを行った。
* CVM用にNVMeを選択した場合は、NVMeのメーカーによってはインストール後、CVMのブートに失敗する。Transcend TS512GMTE110Sだと失敗、Samsung 970 EVOだと成功した。これはTS512GMTE110Sに採用されてるSMIのコントローラチップのバグのせいっぽい。https://bugzilla.kernel.org/show_bug.cgi?id=20205

起動失敗時はQemuのログを見ると下記のようなメッセージが出ていた。
```
qemu-system-x86_64: -device vfio-pci,host=01:00.0,id=hostdev2,bus=pci.0,addr=0x8: vfio error: 0000:01:00.0: failed to add PCI capability 0x11[0x50]@0xb0: table & pba overlap, or they don't fit in BARs, or don't align
```
