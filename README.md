# ansible-vpn

Ansible playbook that deploys a VPN server with wireguard.

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

Edit this example configuration