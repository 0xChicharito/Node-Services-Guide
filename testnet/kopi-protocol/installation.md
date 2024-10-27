# ⚙️ Installation

### Installation Dependencies

```bash
# Install dependencies for building from source
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
sudo apt update
sudo apt install -y curl git jq lz4 build-essential
			
# Install Go
sudo rm -rf /usr/local/go
curl -L https://go.dev/dl/go1.21.6.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile
source .bash_profile
```

### Environment Variable Configuration

```bash
# Fill form to set your env
echo "export MONIKER="Enter Node Name"" >> $HOME/.bash_profile
echo "export WALLET="Enter Wallet"" >> $HOME/.bash_profile
echo "export KopiMoney_CHAIN_ID="kopi-test-5"" >> $HOME/.bash_profile
echo "export KopiMoney_PORT="25"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Download Binary

```bash
cd $HOME
rm -rf kopi
git clone --depth 1 -b v0.6 https://github.com/kopi-money/kopi.git
cd kopi
make install
```

### Config And Init

```bash
kopid init $MONIKER
sed -i -e "s|^node *=.*|node = "tcp://localhost:${PRYSM}657"|" $HOME/.kopid/config/client.toml
```

### Genesis And Addrbook

```bash
wget -O $HOME/.kopid/config/genesis.json https://snapshot.sipalingtestnet.com/kopi/genesis.json
wget -O $HOME/.kopid/config/addrbook.json https://snapshot.sipalingtestnet.com/kopi/addrbook.json
```

### Seeds And Peers

```bash
SEEDS="0bec7d2970be72fd1e8352141a46da3a0398c498@2a01:4f9:6b:2099::2:12656"
PEERS="https://rpc.kopi.sipalingtestnet.com/net_info?"
sed -i -e "/^[p2p]/,/^[/{s/^[[:space:]]*seeds *=.*/seeds = "$SEEDS"/}" -e "/^[p2p]/,/^[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = "$PEERS"/}" $HOME/.kopid/config/config.toml
```

### Costum Port

```bash
# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${PRYSM}317%g;
s%:8080%:${PRYSM}080%g;
s%:9090%:${PRYSM}090%g;
s%:9091%:${PRYSM}091%g;
s%:8545%:${PRYSM}545%g;
s%:8546%:${PRYSM}546%g;
s%:6065%:${PRYSM}065%g" $HOME/.kopid/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${PRYSM}658%g;
s%:26657%:${PRYSM}657%g;
s%:6060%:${PRYSM}060%g;
s%:26656%:${PRYSM}656%g;
s%^external_address = ""%external_address = "$(wget -qO- eth0.me):${PRYSM}656"%;
s%:26660%:${PRYSM}660%g" $HOME/.kopid/config/config.toml
```

### Pruning And Set Gas

```bash
# config pruning
sed -i -e "s/^pruning *=.*/pruning = "custom"/" $HOME/.kopid/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = "100"/" $HOME/.kopid/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = "50"/" $HOME/.kopid/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.0025ukopi"|g' $HOME/.kopid/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.kopid/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = "null"/" $HOME/.kopid/config/config.toml
```

### Service File

```bash
sudo tee /etc/systemd/system/kopid.service > /dev/null <<EOF
[Unit]
Description=KopiMoney node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.kopid
ExecStart=$(which kopid) start --home $HOME/.kopid
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

### Download Snapshot

| Name         | Height | Size      | Block Time           |
| ------------ | ------ | --------- | -------------------- |
| kopi.tar.lz4 | 243450 | 982.00 MB | 10/22/2024, 00:17:33 |

```bash
# reset and download snapshot
kopid tendermint unsafe-reset-all --home $HOME/.kopid
if curl -s --head curl https://snapshot.sipalingtestnet.com/kopi/kopi.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://snapshot.sipalingtestnet.com/kopi/kopi.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.kopid
    else
  echo "no snapshot founded"
fi
```

### Start service

```bash
sudo systemctl daemon-reload
sudo systemctl enable kopid
sudo systemctl restart kopid && sudo journalctl -u kopid -f
```

#### Node Sync Status Checker <a href="#node-sync-status" id="node-sync-status"></a>

```bash
#!/bin/bash
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.kopid/config/config.toml" | cut -d ':' -f 3)
while true; do
  local_height=$(curl -s localhost:$rpc_port/status | jq -r '.result.sync_info.latest_block_height')
  network_height=$(curl -s https://rpc-kopi-t.sychonix.com/status | jq -r '.result.sync_info.latest_block_height')

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

### Create Validator

```bash
cd $HOME
echo "{
"pubkey":{"@type":"/cosmos.crypto.ed25519.PubKey","key":"$(kopid tendermint show-validator | grep -Po '"key":s*"K[^"]*')"},
    "moniker": "moniker",
	"amount": "ukopi",
	"details": "Name validator",
	"commission-rate": "0.1",
  	"commission-max-rate": "0.2",
  	"commission-max-change-rate": "0.01",
    "identity": "",
    "website": "",
    "security": "",
    "min-self-delegation": "1"
}" > validator.json
```

### Send Transaction

```bash
kopid tx staking create-validator $HOME/.kopid/validator.json --from= --chain-id=kopi-test-5 --fees 250000000000000ukopi -y  --gas auto --gas-adjustment 1.6
```
