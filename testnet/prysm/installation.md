---
description: 'Recommended Hardware: 4 Cores, 8GB RAM, 200GB of storage (NVME)'
---

# ⚙️ Installation

<pre class="language-bash"><code class="lang-bash"><strong># install dependencies, if needed
</strong>sudo apt update &#x26;&#x26; sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
</code></pre>

{% hint style="info" %}
Port = 15
{% endhint %}

```bash
# install go, if needed
cd $HOME
VER="1.22.6"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin

# set vars
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="Chicharito | Node 9X"" >> $HOME/.bash_profile
echo "export PRYSM_CHAIN_ID="prysm-devnet-1"" >> $HOME/.bash_profile
echo "export PRYSM_PORT="15"" >> $HOME/.bash_profile
source $HOME/.bash_profile

# download binary
cd $HOME
rm -rf prysm
git clone https://github.com/kleomedes/prysm prysm
cd prysm
git checkout v0.1.0-devnet
make install

# config and init app
prysmd config node tcp://localhost:${PRYSM_PORT}657
prysmd config keyring-backend os
prysmd config chain-id prysm-devnet-1
prysmd init "Chicharito | Node 9X" --chain-id prysm-devnet-1

# download genesis and addrbook
wget -O $HOME/.prysm/config/genesis.json https://snapshot.node9x.com/prysm/genesis.json
wget -O $HOME/.prysm/config/addrbook.json https://snapshot.node9x.com/prysm/addrbook.json

# set seeds and peers
SEEDS="1b5b6a532e24c91d1bc4491a6b989581f5314ea5@prysm-testnet-seed.itrocket.net:25656"
PEERS="ff15df83487e4aa8d2819452063f336269958d09@prysm-testnet-peer.itrocket.net:25657,01e40fe961c9522936a8bb7ede533198614abf9f@[2a0e:dc0:2:2f71::1]:14256,e0daf1e5649feba5ba3787288e66e1c9921b2c4c@149.50.96.153:19756,50dcf516699f45351037d08c0074629a0748d446@[2a03:cfc0:8000:13::b910:277f]:14256,69509925a520c5c7c5f505ec4cedab95073388e5@136.243.13.36:29856,2334e9eb772d5aaf9c48a2885c41d5c33e911912@65.109.92.163:3020,88ad3a3b9b981f0bbb52d5c996d0f7e1aa9426fa@65.108.206.118:61256,66ea180127711b96e35683be6e6f1cffc2b04e0a@184.107.169.193:25656,bc1a37c7656e6f869a01bb8dabaf9ca58fe61b0c@5.9.73.170:29856,170bf5fa23b18d19148ca9a52dbdde485ad59f7b@65.109.79.185:15656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.prysm/config/config.toml


# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${PRYSM_PORT}317%g;
s%:8080%:${PRYSM_PORT}080%g;
s%:9090%:${PRYSM_PORT}090%g;
s%:9091%:${PRYSM_PORT}091%g;
s%:8545%:${PRYSM_PORT}545%g;
s%:8546%:${PRYSM_PORT}546%g;
s%:6065%:${PRYSM_PORT}065%g" $HOME/.prysm/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${PRYSM_PORT}658%g;
s%:26657%:${PRYSM_PORT}657%g;
s%:6060%:${PRYSM_PORT}060%g;
s%:26656%:${PRYSM_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${PRYSM_PORT}656\"%;
s%:26660%:${PRYSM_PORT}660%g" $HOME/.prysm/config/config.toml

# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.prysm/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.prysm/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.prysm/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.0uprysm"|g' $HOME/.prysm/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.prysm/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.prysm/config/config.toml

# create service file
sudo tee /etc/systemd/system/prysmd.service > /dev/null <<EOF
[Unit]
Description=Prysm node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.prysm
ExecStart=$(which prysmd) start --home $HOME/.prysm
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# reset and download snapshot
prysmd tendermint unsafe-reset-all --home $HOME/.prysm
if curl -s --head curl https://snapshot.node9x.com/prysm_testnet.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://snapshot.node9x.com/prysm_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.prysm
    else
  echo "no snapshot founded"
fi

# enable and start service
sudo systemctl daemon-reload
sudo systemctl enable prysmd
sudo systemctl restart prysmd && sudo journalctl -u prysmd -f
```

### Create wallet <a href="#create-wallet" id="create-wallet"></a>

```bash
# to create a new wallet, use the following command. don’t forget to save the mnemonic
prysmd keys add $WALLET

# to restore exexuting wallet, use the following command
prysmd keys add $WALLET --recover

# save wallet and validator address
WALLET_ADDRESS=$(prysmd keys show $WALLET -a)
VALOPER_ADDRESS=$(prysmd keys show $WALLET --bech val -a)
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile

# check sync status, once your node is fully synced, the output from above will print "false"
prysmd status 2>&1 | jq 

# before creating a validator, you need to fund your wallet and check balance
prysmd query bank balances $WALLET_ADDRESS 
```

### Node Sync Status Checker <a href="#node-sync-status" id="node-sync-status"></a>

```bash
#!/bin/bash
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.prysm/config/config.toml" | cut -d ':' -f 3)
while true; do
  local_height=$(curl -s localhost:$rpc_port/status | jq -r '.result.sync_info.latest_block_height')
  network_height=$(curl -s https://prysm-testnet-rpc.itrocket.net/status | jq -r '.result.sync_info.latest_block_height')

  if ! [[ "$local_height" =~ ^[0-9]+$ ]] || ! [[ "$network_height" =~ ^[0-9]+$ ]]; then
    echo -e "\033[1;31mError: Invalid block height data. Retrying...\033[0m"
    sleep 5
    continue
  fi

  blocks_left=$((network_height - local_height))
  if [ "$blocks_left" -lt 0 ]; then
    blocks_left=0
  fi

  echo -e "\033[1;33mNode Height:\033[1;34m $local_height\033[0m \033[1;33m| Network Height:\033[1;36m $network_height\033[0m \033[1;33m| Blocks Left:\033[1;31m $blocks_left\033[0m"

  sleep 5
done
```

### Create validator <a href="#create-validator" id="create-validator"></a>

```bash
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(prysmd comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000uprysm\",
    \"moniker\": \"\",
    \"identity\": \"\",
    \"website\": \"\",
    \"security\": \"\",
    \"details\": \"I love Prysm ❤️\",
    \"commission-rate\": \"0.1\",
    \"commission-max-rate\": \"0.2\",
    \"commission-max-change-rate\": \"0.01\",
    \"min-self-delegation\": \"1\"
}" > validator.json
# Create a validator using the JSON configuration
prysmd tx staking create-validator validator.json \
    --from $WALLET \
    --chain-id prysm-devnet-1 \
    --gas auto --gas-adjustment 1.5 \
    -y
	
```

### Delete node <a href="#delete" id="delete"></a>

```bash
sudo systemctl stop prysmd
sudo systemctl disable prysmd
sudo rm -rf /etc/systemd/system/prysmd.service
sudo rm $(which prysmd)
sudo rm -rf $HOME/.prysm
sed -i "/PRYSM_/d" $HOME/.bash_profile
```

## If cannot use command

```bash
export PRYSMD_NODE="http://localhost:15657"
```

```bash
source ~/.bashrc   # If you are using bash
# or
source ~/.zshrc   # If you are using zsh
```
