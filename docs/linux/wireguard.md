# Wireguard - Simple Client Server Setup

Simple setup of wireguard with server and multiple peer config with pre shared keys

## Operation
Check status of Wireguard
```
wg show
```
### Server
Service is started/stopped with:
```
systemctl start|stop wg-quick@wg0.service
```

Service is enabled/disabled with
```
systemctl enable wg-quick@wg0.service
```
### Client/Peer
Should be managed by Network Manager, after importing it during the setup

## Setup

### Server

Install wireguard

Enable routing by editing `/etc/sysctl.conf`, and making sure this exists:
```
net.ipv4.ip_forward=1
```

Reload config:
```
sysctl -p
```

Go to `/etc/wireguard`, and generate the public and private keys of the server:
```
umask 077; wg genkey | tee server.privatekey | wg pubkey > server.publickey
```

Create `/etc/wireguard/wg0.conf`, and fill in the placeholder values based on the gerneated private key:
``` ini
[Interface]
Address = 10.0.0.1/24
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -A FORWARD -o %i -j ACCEPT; iptables -t nat -A POSTROUTING -o <INTERFACE> -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -D FORWARD -o %i -j ACCEPT; iptables -t nat -D POSTROUTING -o <INTERFACE> -j MASQUERADE
ListenPort = <PORT>
PrivateKey = <server.privatekey>
```

### Client(s)/Peer(s)
Install wireguard

Go to `/etc/wireguard`, and generate the public and private keys of the server, and also the Preshared Key
```
umask 077; wg genkey | tee client.privatekey | wg pubkey > client.publickey 
umask 077; wg genpsk > client.preshared
```

Create `/etc/wireguard/wg0.conf`, and fill in the placeholder values based ont he gereanted keys, and details of the sever:
``` ini
[Interface]
Address = 10.0.0.100/24
PrivateKey = <client.privatekey>

[Peer]
PublicKey = <server.publickey>
AllowedIPs = 10.0.0/24, <OTHER NETWORKS> 
Endpoint = <DOMAIN/IP>:<PORT>
PersistentKeepalive = 15
PresharedKey = <client.preshared>
```

Then Add this config into NetworkManager
```
nmcli connection import type wireguard file /etc/wireguard/wg0.conf
```

### Server Again
Append the below peer details into `/etc/wireguard/wg0.conf`
``` ini
[Peer]
PublicKey = <client.publickey>
AllowedIPs = 10.0.0.100/32
PresharedKey = <client.preshared>
```

The resulting `/etc/wireguard/wg0.conf` on the server should now look like this:
``` ini
[Interface]
Address = 10.0.0.1/24
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -A FORWARD -o %i -j ACCEPT; iptables -t nat -A POSTROUTING -o <INTERFACE> -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -D FORWARD -o %i -j ACCEPT; iptables -t nat -D POSTROUTING -o <INTERFACE> -j MASQUERADE
ListenPort = <PORT>
PrivateKey = <server.privatekey>

[Peer]
PublicKey = <client.publickey>
AllowedIPs = 10.0.0.100/32
PresharedKey = <client.preshared>
```
