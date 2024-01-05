---
title: "Lenovo m75s small gen2でNTPサーバーと同期できない"
description: 
date: 2021-04-02T10:17:25+09:00
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
    - ntp
---
NTPはクライアント側のローカルに持っているハードウェアクロックとNTPサーバ側の時刻情報をしばらく比較した後に、NTPサーバ側がクライアントをrejectするかどうか決めるようである。その時にクライアント側のハードウェアクロックがあまりにも精度が悪いとサーバとの距離が遠いと判断されてflash=400 peer_distとしてrejectされる。jitterの値も1198.290と非常に高い。
```
[root@m75s-1-host ~]# ntpq -c as
ind assid status  conf reach auth condition  last_event cnt
===========================================================
  1 47715  9024   yes   yes  none    reject   reachable  2

[root@m75s-1-host ~]# ntpq -c "rv 47715"
associd=47715 status=9024 conf, reach, sel_reject, 2 events, reachable,
srcadr=raspberrypi.mkashi.com, srcport=123, dstadr=192.168.151.241,
dstport=123, leap=00, stratum=2, precision=-18, rootdelay=3.922,
rootdisp=5.783, refid=133.243.238.164,
reftime=e4118348.66206e7a  Fri, Apr  2 2021 11:49:28.398,
rec=e41183d1.b1b84388  Fri, Apr  2 2021 11:51:45.694, reach=037,
unreach=0, hmode=3, pmode=4, hpoll=6, ppoll=6, headway=1,
flash=400 peer_dist, keyid=0, offset=1869.194, delay=0.979,
dispersion=438.293, jitter=1198.290, xleave=0.265,
filtdelay=     0.98    0.94    0.67    0.43    1.05    0.00    0.00    0.00,
filtoffset= 1869.19 1441.02 1006.12  557.80  109.76    0.00    0.00    0.00,
filtdisp=      0.00    0.96    1.94    2.94    3.95 16000.0 16000.0 16000.0
```
ハードウェアクロックはm75sだとtsc, hpet, acpi_pmの中から選べる。tscが一番精度が高いのでtscがデフォルトになっているようである。ただm75sではtscの精度が悪く、実際の時間に比べてめちゃくちゃ進むのが遅くてNTPサーバから距離が遠いと判断されてしまうようだ。hpetだと問題なく同期できるようになるのでkernelの起動パラメータに`clocksource=hpet`を設定する。

```
[root@m75s-1-host ~]# cat /sys/devices/system/clocksource/clocksource0/available_clocksource
tsc hpet acpi_pm 
[root@m75s-1-host ~]# cat /sys/devices/system/clocksource/clocksource0/current_clocksource
tsc
```

hpetに変更後は下記の通りNTPサーバと同期できるようになった。
```
[root@m75s-1-host ~]# ntpq -c as
ind assid status  conf reach auth condition  last_event cnt
===========================================================
  1 35947  963a   yes   yes  none  sys.peer    sys_peer  3

[root@m75s-1-host ~]# ntpq -c "rv 35947"
associd=35947 status=963a conf, reach, sel_sys.peer, 3 events, sys_peer,
srcadr=raspberrypi.mkashi.com, srcport=123, dstadr=192.168.151.241,
dstport=123, leap=00, stratum=2, precision=-18, rootdelay=4.929,
rootdisp=29.007, refid=133.243.238.164,
reftime=e4125f84.662e55a0  Sat, Apr  3 2021  3:29:08.399,
rec=e4126306.366cee04  Sat, Apr  3 2021  3:44:06.212, reach=377,
unreach=0, hmode=3, pmode=4, hpoll=9, ppoll=9, headway=0, flash=00 ok,
keyid=0, offset=0.164, delay=0.866, dispersion=11.731, jitter=0.211,
xleave=0.235,
filtdelay=     1.02    0.87    1.11    0.99    1.80    1.01    0.98    1.01,
filtoffset=    0.24    0.16    0.08   -0.12    0.15   -0.09   -0.09   -0.13,
filtdisp=      0.01    4.03    8.08   12.07   16.12   19.99   23.86   27.86
```

kernel 4.19.84-2.el7.nutanix.20190916.276.x86_64ではtscで同期できなかったが、5.8.0-55-genericでは特に問題なくデフォルトのままで同期できた。kernelのバグかもしれない。

参考: http://log.or.cz/?p=80
