---
description: 'Recommended Hardware: 4 Cores, 32GB RAM, 1000GB of storage (NVME)'
---

# ⚙️ Installation

```bash
# install dependencies, if needed
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

```bash
# install go, if needed
cd $HOME
VER="1.22.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin

# set vars
# PORT = 37
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export FIAMMA_CHAIN_ID="fiamma-testnet-1"" >> $HOME/.bash_profile
echo "export FIAMMA_PORT="37"" >> $HOME/.bash_profile
source $HOME/.bash_profile

# download binary
cd $HOME
rm -rf fiamma
git clone https://github.com/fiamma-chain/fiamma.git
cd fiamma
git checkout v1.0.0
make install

# config and init app
fiammad init $MONIKER --chain-id $FIAMMA_CHAIN_ID
sed -i -e "s|^node *=.*|node = \"tcp://localhost:${FIAMMA_PORT}657\"|" $HOME/.fiamma/config/client.toml
sed -i -e "s|^keyring-backend *=.*|keyring-backend = \"os\"|" $HOME/.fiamma/config/client.toml
sed -i -e "s|^chain-id *=.*|chain-id = \"fiamma-testnet-1\"|" $HOME/.fiamma/config/client.toml

# download genesis and addrbook
wget -O $HOME/.fiamma/config/genesis.json https://server-5.itrocket.net/testnet/fiamma/genesis.json
wget -O $HOME/.fiamma/config/addrbook.json  https://server-5.itrocket.net/testnet/fiamma/addrbook.json

# set seeds and peers
SEEDS="1e8777199f1edb3a35937e653b0bb68422f3c931@fiamma-testnet-seed.itrocket.net:50656"
PEERS="16b7389e724cc440b2f8a2a0f6b4c495851934ff@fiamma-testnet-peer.itrocket.net:49656,bf2c0595ffb5af59a97febf092720bd841ee0a54@65.109.84.235:50656,eb63a70227f4e6c482e70fffbcd0bdcdfa9b65f3@185.248.24.16:37656,d91f18353d2371386360e8dacc58bb706abfa955@198.7.127.79:50656,2f1f3bee3f9c946d1f91f078de09e313956a618e@161.97.167.196:20656,dcf7fe367205140540df50bfb246488d8d175235@45.159.220.147:30656,74cb71245d2dbaafc9688a77540604a0427e30fe@65.21.232.175:50656,c0bb1182753ed71aa22b1427b8265e782cab260a@135.181.139.249:41656,1c9def41279dfc0fe2cc7c8de683d852f70bb2fc@195.26.241.17:26656,4c8315f5dd83b4b066fd2471a9bf37e790bbdcb5@65.109.24.155:20156,e1e7776ae71f38424ae8ccf3c1ccc0d303d308c0@144.91.124.126:29656,48cc8b28f2b7d07263936e5e2a7130bb01df1872@84.247.187.77:37656,cd574a3d8c022e8a6f19b010230069ba9987d905@164.132.247.253:56396,7f607b5d6a5885888f65f219440c07867eee72ff@23.88.5.169:22656,5ead2fb9ef45dd786cc9bb805400e0a75037f7f8@135.125.97.162:30256"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.fiamma/config/config.toml

# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${FIAMMA_PORT}317%g;
s%:8080%:${FIAMMA_PORT}080%g;
s%:9090%:${FIAMMA_PORT}090%g;
s%:9091%:${FIAMMA_PORT}091%g;
s%:8545%:${FIAMMA_PORT}545%g;
s%:8546%:${FIAMMA_PORT}546%g;
s%:6065%:${FIAMMA_PORT}065%g" $HOME/.fiamma/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${FIAMMA_PORT}658%g;
s%:26657%:${FIAMMA_PORT}657%g;
s%:6060%:${FIAMMA_PORT}060%g;
s%:26656%:${FIAMMA_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${FIAMMA_PORT}656\"%;
s%:26660%:${FIAMMA_PORT}660%g" $HOME/.fiamma/config/config.toml

# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.fiamma/config/app.toml 
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.fiamma/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.fiamma/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.00001ufia"|g' $HOME/.fiamma/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.fiamma/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.fiamma/config/config.toml

# create service file
sudo tee /etc/systemd/system/fiammad.service > /dev/null <<EOF
[Unit]
Description=Fiamma node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.fiamma
ExecStart=$(which fiammad) start --home $HOME/.fiamma
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# reset and download snapshot
fiammad tendermint unsafe-reset-all --home $HOME/.fiamma
if curl -s --head curl curl -i https://snapshot.node9x.com/fiamma_testnet.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl curl -i https://snapshot.node9x.com/fiamma_testnet.tar.lz4
 | lz4 -dc - | tar -xf - -C $HOME/.fiamma
    else
  echo "no snapshot found"
fi

# enable and start service
sudo systemctl daemon-reload
sudo systemctl enable fiammad
sudo systemctl restart fiammad && sudo journalctl -u fiammad -f
```

### Create wallet <a href="#create-wallet" id="create-wallet"></a>

```bash
# to create a new wallet, use the following command. don’t forget to save the mnemonic
fiammad keys add $WALLET

# to restore exexuting wallet, use the following command
fiammad keys add $WALLET --recover

# save wallet and validator address
WALLET_ADDRESS=$(fiammad keys show $WALLET -a)
VALOPER_ADDRESS=$(fiammad keys show $WALLET --bech val -a)
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile

# check sync status, once your node is fully synced, the output from above will print "false"
fiammad status 2>&1 | jq 

# before creating a validator, you need to fund your wallet and check balance
fiammad query bank balances $WALLET_ADDRESS 
```

### Node Sync Status Checker <a href="#node-sync-status" id="node-sync-status"></a>

```bash
#!/bin/bash
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.fiamma/config/config.toml" | cut -d ':' -f 3)
while true; do
  local_height=$(curl -s localhost:$rpc_port/status | jq -r '.result.sync_info.latest_block_height')
  network_height=$(curl -s https://fiamma-testnet-rpc.itrocket.net/status | jq -r '.result.sync_info.latest_block_height')

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

MonikerIdentityDetailsAmount, ufiaCommission rateCommission max rateCommission max change rateWebsite

```bash
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(fiammad comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000ufia\",
    \"moniker\": \"test\",
    \"identity\": \"\",
    \"website\": \"\",
    \"security\": \"\",
    \"details\": \"I love blockchain ❤️\",
    \"commission-rate\": \"0.1\",
    \"commission-max-rate\": \"0.2\",
    \"commission-max-change-rate\": \"0.01\",
    \"min-self-delegation\": \"1\"
}" > validator.json
# Create a validator using the JSON configuration
fiammad tx staking create-validator validator.json \
    --from $WALLET \
    --chain-id fiamma-testnet-1 \
	--gas auto --gas-adjustment 1.5
	
```

### Delete node <a href="#delete" id="delete"></a>

```bash
sudo systemctl stop fiammad
sudo systemctl disable fiammad
sudo rm -rf /etc/systemd/system/fiammad.service
sudo rm $(which fiammad)
sudo rm -rf $HOME/.fiamma
sed -i "/FIAMMA_/d" $HOME/.bash_profile
```
