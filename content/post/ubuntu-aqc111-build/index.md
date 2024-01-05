---
title: "Ubuntu 20.04でaqc111のビルド"
description: 
date: 2020-11-01T10:18:25+09:00
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
    - aqc111
---
QNAP QNA-UC5G1Tのドライバをdkmsを使って自動ビルドする。
[Aquantia](https://www.marvell.com/support/downloads.html)からaqc111の1.3.3.0を持ってきて、展開する。
Makefileを書き換えないとビルドに失敗するので以下のように書き換える
```diff
--- aqc111-1.3.3.0/Makefile     2019-07-02 18:36:06.000000000 +0900
+++ aqc111-1.3.3.1/Makefile     2020-10-05 23:57:00.873643349 +0900
@@ -23,7 +23,7 @@
 obj-m      :=  $(TARGET).o

 default:
-       make -C $(BUILD_DIR) SUBDIRS=$(PWD) modules
+       make -C $(BUILD_DIR) M=$(shell pwd) modules

 $(TARGET).o: $(OBJS)
        $(LD) $(LD_RFLAG) -r -o $@ $(OBJS)
@@ -32,7 +32,7 @@
        cp -v $(TARGET).ko $(DEST) && /sbin/depmod -a

 clean:
-       $(MAKE) -C $(BUILD_DIR) SUBDIRS=$(PWD) clean
+       $(MAKE) -C $(BUILD_DIR) M=$(shell pwd) clean

 .PHONY: modules clean
```
[このパッチを当てる](/p/aqc111でbondを使えるようにする/)

ソースコードと同じディレクトリに下記のdkms.confを作る
```
PACKAGE_NAME="aqc111"
PACKAGE_VERSION="1.3.3.1"
BUILT_MODULE_NAME[0]="aqc111"
DEST_MODULE_LOCATION[0]="/kernel/drivers/net/usb"
AUTOINSTALL="yes"
```

ソースコードのディレクトリを/usr/src/aqc111-1.3.3.1に移動
```
apt -y install dkms
dkms add -m aqc111 -v 1.3.3.1
dkms build -m aqc111 -v 1.3.3.1
dkms install -m aqc111 -v 1.3.3.1
dkms status
```
