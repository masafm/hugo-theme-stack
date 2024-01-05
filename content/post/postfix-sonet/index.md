---
title: "Postfixでso-netのメールサーバ経由で送信"
description: 
date: 2020-10-25T10:19:25+09:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - Linux
tags:
    - postfix
    - so-net
---
# インストール
CentOS7,8
`yum install -y postfix cyrus-sasl cyrus-sasl-plain mailx`

Debian
`apt install -y postfix libsasl2-2 libsasl2-modules bsd-mailx`

# 設定
## /etc/postfix/main.cf に以下を追記
```
relayhost = [mail.so-net.ne.jp]:587
smtp_sasl_auth_enable = yes
smtp_sasl_mechanism_filter = plain
smtp_sasl_password_maps = hash:/etc/postfix/so-net_passwd
smtp_sasl_security_options = noanonymous
smtp_sasl_tls_security_options = noanonymous
```
## /etc/postfix/so-net_passwd を作成し、下記内容を記載
```
[mail.so-net.ne.jp]:587 ユーザID:パスワード
postmap hash:/etc/postfix/so-net_passwd
```
## Postfix再起動
必要に応じて/etc/alias編集
```
systemctl enable postfix;
systemctl restart postfix;
newaliases;
```
