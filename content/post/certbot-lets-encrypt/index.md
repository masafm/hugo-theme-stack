---
title: "CertbotによるLet’s Encryptの自動更新"
description: 
date: 2021-01-23T10:17:25+09:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - Linux
tags:
    - certbot
---
自分で運用しているDNSサーバを使ってCertbotによるLet’s Encryptの証明書の自動更新を行う。
下記のcertbot-renew.shを1日に1回など定期的に実行する。deploy-hookは使ってるhttpdをリロードするコマンドを入れる。
```sh
#!/bin/sh

/usr/bin/certbot renew --preferred-challenges dns-01 --manual-auth-hook /root/bin/auth-hook.sh --manual-cleanup-hook /root/bin/clean-hook.sh --deploy-hook "systemctl reload apache2"
```

auth-hook.sh: DNSサーバにTXTレコードを仕込むシェルスクリプト
```sh
#!/bin/sh

echo "_acme-challenge.${CERTBOT_DOMAIN}. IN TXT \"${CERTBOT_VALIDATION}\"" | ssh dns "cat >>/etc/bind/mkashi.com.zone"
ssh dns systemctl restart bind9
ssh dns cat /etc/bind/mkashi.com.zone
```

clean-hook.sh: DNSサーバからTXTレコードを削除するスクリプト
```sh
#!/bin/sh

ssh dns "sed -i -e '/_acme-challenge.${CERTBOT_DOMAIN}. IN TXT \"${CERTBOT_VALIDATION}\"/d' /etc/bind/mkashi.com.zone"
ssh dns systemctl restart bind9
ssh dns cat /etc/bind/mkashi.com.zone
```
