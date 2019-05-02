# Installing Wireguard VPN

## Prerequisites

This doc assumes the following:
- Debian 9

## Installation

### Step 1: Install basic packages

Make sure system is updated
```sh
sudo apt update
sudo apt upgrade
```

Install `ufw` and `unattended-upgrades`
```sh
sudo apt install ufw
sudo apt install unattended-upgrades
```

### Step 2: Modify apt to include debian unstable

Add Debian Unstable to `/etc/apt/sources.list.d/unstable.list`
```sh
sudo nano /etc/apt/sources.list.d/unstable.list
```

Insert the following lines into the file
```sh
# /etc/apt/sources.list.d/unstable.list
deb http://deb.debian.org/debian/ unstable main
```

Change apt preferences
```sh
sudo nano /etc/apt/preferences.d/limit-unstable
```

Insert the following lines into the file
```sh
# /etc/apt/preferences.d/limit-unstable
Package: *
Pin: release a=unstable
Pin-Priority: 90
```

### Step 3: Install Wireguard

```sh
sudo apt update
sudo apt install wireguard
```

Verify that `wireguard` directory is created in `/etc/`
```sh
sudo ls /etc
```

Verify that kernel is loaded properly
```sh
sudo modprobe wireguard
lsmod | grep wireguard
```

### Step 4: Generate keys

Run the following command
```sh
wg genkey | tee privatekey | wg pubkey > publickey
```

Verify that the keys were correctly generated
```sh
sudo cat privatekey
sudo cat publickey
```

### Step 5: Enable ip forwarding

Check if you have it enabled:
```sh
sudo cat /proc/sys/net/ipv4/ip_forward
```

If the previous command returns `0`, edit the `/etc/sysctl.conf` file
```sh
sudo nano /etc/sysctl.conf
```

Uncomment the following line:
```sh
net.ipv4.ip_forward=1
```

### Step 6: Setup firewall settings

Assuming port `51820/udp` is to be used
```sh
sudo ufw allow 22/tcp
sudo ufw allow 51820/udp
```

Enabling `ufw`: Make sure `22/tcp` is allowed or you may lose connection to the server
```sh
sudo ufw enable
```

Check if `ufw` is running correctly with the correct firewall rules
```sh
sudo ufw status verbose
```

*Note: Make sure to configure port forwarding at your router for VPN to accept incoming connections*

### Step 7: Configuration

Create `wg0.conf` configuration file in `/etc/wireguard`
```sh
sudo nano /etc/wireguard/wg0.conf
```

Use the following configuration format and adjust accordingly
```sh
# /etc/wireguard/wg0.conf
[Interface]
PrivateKey = <SERVER_PRIVATE_KEY>
Address = <SERVER_TUNNEL_IP>/32
ListenPort = 51820
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -A FORWARD -o wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE; ip6tables -A FORWARD -i wg0 -j ACCEPT; ip6tables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -D FORWARD -o wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE; ip6tables -D FORWARD -i wg0 -j ACCEPT; ip6tables -t nat -D POSTROUTING -o eth0 -j MASQUERADE

[Peer]
PublicKey = <CLIENT_PUBLIC_KEY>
AllowedIPs = <CLIENT_TUNNEL_IP>/32

[Peer]
PublicKey = <CLIENT_PUBLIC_KEY>
AllowedIPs = <CLIENT_TUNNEL_IP>/32
```

Enable `wg0`
```sh
sudo wg-quick up wg0
```

Verify that `wg0` tunnel interface is working properly
```sh
sudo wg show
```

### Enabling `wg0` on boot

If `wg0` is not already turned on, you can run:
```sh
sudo systemctl start wg-quick@wg0
```

Enable restart on boot
```sh
sudo systemctl enable wg-quick@wg0
```

## Client/Mobile Configuration

### Full Tunnel
```sh
# /etc/wireguard/wg0.conf
[Interface]
PrivateKey = <CLIENT_PRIVATE_KEY>
Address = <CLIENT_TUNNEL_IP>/32
ListenPort = 51820
DNS = <SERVER_LAN_DNS_SERVER>

[Peer]
PublicKey = <SERVER_PUBLIC_KEY>
AllowedIPs = 0.0.0.0/0
Endpoint = <SERVER_FQDN>:<SERVER_PORT>
PersistentKeepAlive = 25
```

### Split Tunnel
Note the changes in `AllowedIPs` and its CIDR range
```sh
# /etc/wireguard/wg0.conf
[Interface]
PrivateKey = <CLIENT_PRIVATE_KEY>
Address = <CLIENT_TUNNEL_IP>/32
ListenPort = 51820
DNS = <SERVER_LAN_DNS_SERVER>

[Peer]
PublicKey = <SERVER_PUBLIC_KEY>
AllowedIPs = <SERVER_LAN_IP>/24
Endpoint = <SERVER_FQDN>:<SERVER_PORT>
PersistentKeepAlive = 25
```

## References
- [Debian Wireguard Guide](https://wiki.debian.org/Wireguard)
- [Arch Linux Wireguard Guide](https://wiki.archlinux.org/index.php/WireGuard)
- [Wireguard Quickstart Guide](https://www.wireguard.com/quickstart/)
- [Wireguard Linode Guide](https://www.linode.com/docs/networking/vpn/set-up-wireguard-vpn-on-ubuntu/)
- [Reddit Wireguard Walkthrough](https://www.reddit.com/r/sysadmin/comments/9dime7/wireguard_vpn_walkthrough/)
- [Reddit Server Setup](https://www.reddit.com/r/WireGuard/comments/9hu6lv/wgquick_kills_internet/)