---
title: "Wireguardセットアップメモ"
description: 
date: 2024-02-09T10:09:53+09:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - Linux
tags:
    - wireguard
---
# Generate config
```
mkdir server client
wg genkey | tee server/server.key | wg pubkey > server/server.pub
wg genkey | tee client/client.key | wg pubkey > client/client.pub

cat <<EOF >server/wg0.conf
[Interface]
ListenPort = <PORT>
PrivateKey = $(cat server/server.key) 
Address = 169.254.255.1/28
PostUp = iptables -t nat -A POSTROUTING -o ens5 -s 169.254.255.0/24 -j MASQUERADE
PostDown = iptables -t nat -D POSTROUTING -o ens5 -s 169.254.255.0/24 -j MASQUERADE

[Peer] #Mac
PublicKey = $(cat client/client.pub) 
AllowedIPs = 169.254.255.2/32
EOF

cat <<EOF >client/wg0.conf
[Interface]
PrivateKey = $(cat client/client.key) 
Address = 169.254.255.2/28

[Peer]
PublicKey = $(cat server/server.pub) 
AllowedIPs = 169.254.255.0/28,<YOUR SUBNET>
Endpoint = <ENDPOIND>:<PORT>
PersistentKeepalive = 25
EOF
```

# Server Side
Copy server/wg0.conf to /etc/wireguard/
```
systemctl enable wg-quick@wg0
systemctl start wg-quick@wg0
```

# Client Side
```
brew install wireguard-go wireguard-tools
```
Copy client/wg0.conf to /opt/homebrew/etc/wireguard/
```
sudo wg-quick up wg0
sudo wg show 
```

# AWS ENI settings
Disable source/destination check
