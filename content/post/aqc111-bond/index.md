---
title: "aqc111でbondを使えるようにする"
description: 
date: 2020-11-01T10:17:25+09:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - Linux
tags:
    - aqc111
    - bond
---
Aquantiaで公開されている1.3.3.0とUbuntu 20.04のkernel 5.4.0に含まれてるaqc111ドライバではbondがうまく動かないので以下のパッチを当てる必要がある。
```diff
--- aqc111-1.3.3.0/aqc111.c     2019-07-02 18:36:06.000000000 +0900
+++ aqc111-1.3.3.1/aqc111.c     2020-10-07 21:50:30.412367747 +0900
@@ -21,7 +21,7 @@
 #include "aq_compat.h"
 #include "aqc111.h"

-#define DRIVER_VERSION "1.3.3.0"
+#define DRIVER_VERSION "1.3.3.1"
 #define DRIVER_NAME "aqc111"

 static int aqc111_read_cmd_nopm(struct usbnet *dev, u8 cmd, u16 value,
@@ -724,17 +724,23 @@
 }

 static int aqc111_set_mac_addr(struct net_device *net, void *p)
-{
-       struct usbnet *dev = netdev_priv(net);
-       int ret = 0;

-       ret = eth_mac_addr(net, p);
-       if (ret < 0)
-               return ret;
+{
+  struct usbnet *dev = netdev_priv(net);
+  int ret = 0;

-       /* Set the MAC address */
-       return aqc111_write_cmd(dev, AQ_ACCESS_MAC, SFR_NODE_ID, ETH_ALEN,
-                               ETH_ALEN, net->dev_addr);
+  ret = eth_mac_addr(net, p);
+  if (ret < 0)
+    return ret;
+
+  /* Set the MAC address */
+  ret = aqc111_write_cmd(dev, AQ_ACCESS_MAC, SFR_NODE_ID, ETH_ALEN,
+                        ETH_ALEN, net->dev_addr);
+
+  if (ret < 0)
+    return ret;
+
+  return (ret == ETH_ALEN) ? 0 : -1;
 }

 static int aqc111_vlan_rx_kill_vid(struct net_device *net,
@@ -1013,6 +1019,7 @@
        dev->net->hw_features |= AQ_SUPPORT_HW_FEATURE;
        dev->net->features |= AQ_SUPPORT_FEATURE;
        dev->net->vlan_features |= AQ_SUPPORT_VLAN_FEATURE;
+       dev->net->priv_flags |= IFF_LIVE_ADDR_CHANGE;

        aqc111_read_fw_version(dev, aqc111_data);
        aqc111_data->autoneg = AUTONEG_ENABLE;
```

