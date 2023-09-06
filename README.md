# ansible-vpn

There's two parts to operating a Wireguard VPN server.
The first is creating the Wireguard tunnel. 
The second is sending all traffic to the tunnel on the client, and forwarding all the traffic on the server.
This ansible playbook only configures the server, but adding a client should be easy.

What it does:
- Installs wireguard and nftables (iptables won't conflict since they use nftables Debian 10)
- Adds a wireguard configuration based on the clients in clients.yml
- Enables the net.ipv4.ip_forward kernel parameter 
- Configures nftables rules that adds NAT to ethernet interfaces
- Also adds nftables rules for a firewall

## Usage

### Deploy Wireguard to the server:

```
# create server key combo
sk=$(wg genkey); pk=$(wg pubkey <<< $sk); echo -e "sk: $sk\npk: $pk"

# type "server_wg_private_key: the_value_of_sk"
ansible-vault create secrets.yml

# add your own clients and their public keys
vim clients.yml

# run the playbook
ansible-playbook --ask-vault-pass -i inv main.yml
```

### Connect with your client

Here's an example config. Note the 0.0.0.0/0
```
[Interface]
Address = 10.0.0.2/32
PostUp = wg set %i private-key /root/.ssh/wg.priv

[Peer]
PublicKey = rKDhEjqys51Qr6J5dGZyOW0FoB0C0sE1U2b/AmDXiwY=
Endpoint = debibox:51000
AllowedIPs = 0.0.0.0/0
#AllowedIPs = 10.0.0.1/32
PersistentKeepalive = 25
```
Remember to use a nameserver that isn't your router, since that might point to nameservers in your country