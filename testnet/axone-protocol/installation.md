# ⚙️ Installation

## Server preparation <a href="#server-preparation" id="server-preparation"></a>

```bash
apt update && apt upgrade -y
```

## **Install GO**

```bash
# install go, if needed
ver="1.23.1"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```

## Node installation <a href="#node-installation" id="node-installation"></a>

```bash
git clone https://github.com/axone-protocol/axoned && cd axoned
git checkout v10.0.0
make install

axoned version --long | grep -e version -e commit
#version: 10.0.0
#commit: 2f0f84d369852bdb178e299a88c1b8eeb0654b8e
```

## **Download Genesis**

```bash
wget -O $HOME/.axoned/config/genesis.json "https://raw.githubusercontent.com/axone-protocol/networks/main/chains/dentrite-1/genesis.json"

sha256sum ~/.axoned/config/genesis.json
# da1434f8dd7692f093808676e6a43ac37b25ba22fd7c893ac855da2445dc3ec6
```

```bash
# set vars
cd $HOME
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export AXONE_CHAIN_ID="axone-dentrite-1"" >> $HOME/.bash_profile
echo "export AXONE_PORT="10"" >> $HOME/.bash_profile
source $HOME/.bash_profile

# config and init app
axoned init $MONIKER --chain-id $AXONE_CHAIN_ID
axoned config set client chain-id $AXONE_CHAIN_ID
axoned config set client node tcp://localhost:${AXONE_PORT}657

# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${AXONE_PORT}317%g;
s%:8080%:${AXONE_PORT}080%g;
s%:9090%:${AXONE_PORT}090%g;
s%:9091%:${AXONE_PORT}091%g;
s%:8545%:${AXONE_PORT}545%g;
s%:8546%:${AXONE_PORT}546%g;
s%:6065%:${AXONE_PORT}065%g" $HOME/.axoned/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${AXONE_PORT}658%g;
s%:26657%:${AXONE_PORT}657%g;
s%:6060%:${AXONE_PORT}060%g;
s%:26656%:${AXONE_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${AXONE_PORT}656\"%;
s%:26660%:${AXONE_PORT}660%g" $HOME/.axoned/config/config.toml

# set minimum gas price, add peers
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025uaxone\"/;" ~/.axoned/config/app.toml
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:10656\"/" $HOME/.axoned/config/config.toml
peers="d89568d0fda69b1951a433f5f5ff887213a41305@5.9.73.170:17656,72ddc04c09160a58289f9a7c5b29aac358635178@142.132.202.50:38656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.axoned/config/config.toml

# Set up indexer
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.axoned/config/config.toml

# config pruning
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.axoned/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.axoned/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.axoned/config/app.toml
```

## Create a service file and start service

```bash
tee /etc/systemd/system/axoned.service > /dev/null <<EOF
[Unit]
Description=axoned
After=network-online.target

[Service]
User=$USER
ExecStart=$(which axoned) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

#reset and download snapshot
axoned tendermint unsafe-reset-all --home $HOME/.axoned
if curl -s --head curl https://snapshot.node9x.com/axone_testnet.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://snapshot.node9x.com/axone_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.axone
    else
  echo "no snapshot founded"
fi

# enable and start service
systemctl daemon-reload
systemctl enable axoned
systemctl restart axoned && journalctl -u axoned -f -o cat
```

## Create wallet <a href="#create-wallet" id="create-wallet"></a>

```bash
# to create a new wallet, use the following command. don’t forget to save the mnemonic
axoned keys add $WALLET

# to restore exexuting wallet, use the following command
axoned keys add $WALLET --recover

# save wallet and validator address
WALLET_ADDRESS=$(axoned keys show $WALLET -a)
VALOPER_ADDRESS=$(axoned keys show $WALLET --bech val -a)
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile

# check sync status, once your node is fully synced, the output from above will print "false"
axoned status 2>&1 | jq 

# before creating a validator, you need to fund your wallet and check balance
axoned query bank balances $WALLET_ADDRESS 
```

## Claim token on faucet <a href="#node-sync-status" id="node-sync-status"></a>

[https://faucet.axone.xyz/](https://faucet.axone.xyz/)

## Node Sync Status Checker <a href="#node-sync-status-1" id="node-sync-status-1"></a>

```bash
#!/bin/bash
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.axoned/config/config.toml" | cut -d ':' -f 3)
while true; do
  local_height=$(curl -s localhost:$rpc_port/status | jq -r '.result.sync_info.latest_block_height')
  network_height=$(curl -s https://api.dentrite.axone.xyz/rpc/status | jq -r '.result.sync_info.latest_block_height')

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

## Create validator <a href="#create-validator" id="create-validator"></a>

```bash
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(axoned comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"950000uaxone\",
    \"moniker\": \"\",
    \"identity\": \"\",
    \"website\": \"\",
    \"security\": \"",
    \"details\": \"I love blockchain ❤️\",
    \"commission-rate\": \"0.1\",
    \"commission-max-rate\": \"0.2\",
    \"commission-max-change-rate\": \"0.01\",
    \"min-self-delegation\": \"1\"
}" > validator.json
# Create a validator using the JSON configuration
axoned tx staking create-validator /root/validator.json \
    --from Wallet \
    --chain-id axone-dentrite-1 \
    --fees 500uaxone 
```

## Delete node <a href="#delete-1" id="delete-1"></a>

```bash
sudo systemctl stop axoned
sudo systemctl disable axoned
sudo rm -rf /etc/systemd/system/axoned.service
sudo rm $(which axoned)
sudo rm -rf $HOME/.axoned
sed -i "/AXONE_/d" $HOME/.bash_profile
```



