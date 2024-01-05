---
title: "PacemakerでリソースのHAを監視する"
description: 
date: 2021-05-30T10:17:25+09:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - Linux
tags:
    - pacemaker
    - 
---
ノードごとにMailToリソースを作って各ノードに割り当てる。
mailコマンドが必要なので入ってない場合は事前に入れる。
```
# ノード1の監視
pcs resource create MailTo1 ocf:heartbeat:MailTo email="<メールアドレス>"
pcs constraint location MailTo1 prefers <ノード1>
# ノード2の監視
pcs resource create MailTo2 ocf:heartbeat:MailTo email="<メールアドレス>"
pcs constraint location MailTo2 prefers <ノード2>
# ノード3の監視
pcs resource create MailTo1 ocf:heartbeat:MailTo email="<メールアドレス>"
pcs constraint location MailTo3 prefers <ノード3>
```
