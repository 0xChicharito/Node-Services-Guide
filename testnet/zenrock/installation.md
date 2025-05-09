---
description: >-
  Official Documentation Recommended Hardware: 4 Cores, 8GB RAM, 200GB of
  storage (NVME)
---

# ⚙️ Installation

### Manual Installation <a href="#installation" id="installation"></a>

```bash
# install dependencies, if needed
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

```bash
# install go, if needed
cd $HOME
VER="1.23.1"
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
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export ZENROCK_CHAIN_ID="gardia-5"" >> $HOME/.bash_profile
echo "export ZENROCK_PORT="56"" >> $HOME/.bash_profile
source $HOME/.bash_profile

# download binary
wget -O zenrockd.zip https://github.com/Zenrock-Foundation/zrchain/releases/download/v6.3.3/zenrockd.zip
rm zenrockd.zip
chmod +x $HOME/zenrockd
sudo mv $HOME/zenrockd $HOME/go/bin/

# config and init app
zenrockd init $MONIKER --chain-id $ZENROCK_CHAIN_ID
zenrockd config set client chain-id $ZENROCK_CHAIN_ID
zenrockd config set client node tcp://localhost:${ZENROCK_PORT}657

# download genesis and addrbook
wget -O $HOME/.zrchain/config/genesis.json https://snapshot.node9x.com/zrchain/genesis.json
wget -O $HOME/.zrchain/config/addrbook.json https://snapshot.node9x.com/zrchain/addrbook.json

# set seeds and peers
SEEDS="50ef4dd630025029dde4c8e709878343ba8a27fa@zenrock-testnet-seed.itrocket.net:56656"
PEERS="5458b7a316ab673afc34404e2625f73f0376d9e4@65.108.132.123:11656,a412c87699b151a02d1385cbb68b6df396a81c3f@95.217.196.224:26656,57367027b921e75c2f33a4f81b7273961e48ada0@65.109.115.100:27363,b3a09f169f190cdef11ba148c3ac964531928aae@65.108.111.225:36656,5ee4531fd7e3abfe0c5daa15664ff3b0a2f64a4a@65.21.47.120:42656,e1ff342fb55293384a5e92d4bd3bed82ecee4a60@65.108.234.158:26356,c5ea24bbce72051cac5d2cb5db3ee9701d2ac3e9@27.72.31.207:56656,ee7d09ac08dc61548d0e744b23e57436b8c477fc@65.109.93.152:26906,10100d10c3bbbbfc4bd9c607b24802657cbd5709@69.67.150.107:56656,5d73f21cc3be1d19b0b69e37c575126923b55bc8@65.109.113.251:20656,195382bd2d6b4b88ac81d1611ee0d576006416bb@149.102.139.50:56656,c3239230e392195acf86edd6bb4811148af9f399@195.201.168.95:56656,a75e49f5185b6b31f0f14cc1385960347a369b2b@159.69.109.34:56656,1629c4759816afc7bbd3d7f279564e9e26dae850@149.50.110.9:26656,9aa8dde122c3fb10bfbb74eb073e86a914aadd83@65.109.112.148:26656,b61b3bb2b4a66808e78a7c117b92a4c583f89165@213.239.192.18:18256,99fd256c7c32c020ddeb6de56db72cbc56857618@5.9.147.185:26656,53ce8550258ebd7b23dde782919e83c257f7c4e4@207.188.6.109:56656,967c7926e64e4ec2db456d1fc0c93065922e89f6@14.243.48.105:56656,d5519e378247dfb61dfe90652d1fe3e2b3005a5b@213.239.207.162:18256,02a341b450570a74618cc5b148aa73868969039d@158.220.96.82:656,6e2b3de14694e49d3b660404fb43373a52b2e005@185.16.39.190:14156,4fedcc7b30678bd83285722ea09853dad384373c@184.107.169.193:26656,400b7677af8494ea58d722a4f40c54de98b2ed22@8.52.201.252:26656,ade6ea25c70e32ae0fcbc2af0b70000c59daaed5@141.95.151.107:26656,39bf4210b1e47b9df1de0d30a6ab94e18b7c4e9f@185.16.39.127:14156,f18012717325e909d228e67797b5de9239502a37@94.130.35.120:20656,793a1e7cf62a269844e59e3af73e92ae6e2ad4a6@65.109.80.26:26656,edd17ae6c662cf5552e6aaa45c4c7d63a9148c43@37.27.54.184:45656,ace905113977a21ff5e6f134ab95b959d95482ed@144.217.68.182:18256,a98484ac9cb8235bd6a65cdf7648107e3d14dab4@95.217.74.22:18256,1dfbd854bab6ca95be652e8db078ab7a069eae6f@52.30.152.47:26656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.zrchain/config/config.toml


# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${ZENROCK_PORT}317%g;
s%:8080%:${ZENROCK_PORT}080%g;
s%:9090%:${ZENROCK_PORT}090%g;
s%:9091%:${ZENROCK_PORT}091%g;
s%:8545%:${ZENROCK_PORT}545%g;
s%:8546%:${ZENROCK_PORT}546%g;
s%:6065%:${ZENROCK_PORT}065%g" $HOME/.zrchain/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${ZENROCK_PORT}658%g;
s%:26657%:${ZENROCK_PORT}657%g;
s%:6060%:${ZENROCK_PORT}060%g;
s%:26656%:${ZENROCK_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${ZENROCK_PORT}656\"%;
s%:26660%:${ZENROCK_PORT}660%g" $HOME/.zrchain/config/config.toml

# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.zrchain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.zrchain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.zrchain/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0urock"|g' $HOME/.zrchain/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.zrchain/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.zrchain/config/config.toml

# create service file
sudo tee /etc/systemd/system/zenrockd.service > /dev/null <<EOF
[Unit]
Description=Zenrock node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.zrchain
ExecStart=$(which zenrockd) start --home $HOME/.zrchain
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# reset and download snapshot
zenrockd tendermint unsafe-reset-all --home $HOME/.zrchain
if curl -s --head curl https://snapshot.node9x.com/zenrock_testnet.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://snapshot.node9x.com/zenrock_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.zrchain
    else
  echo "no snapshot founded"
fi

# enable and start service
sudo systemctl daemon-reload
sudo systemctl enable zenrockd
sudo systemctl restart zenrockd && sudo journalctl -u zenrockd -f
```

### Create wallet <a href="#create-wallet" id="create-wallet"></a>

```bash
# to create a new wallet, use the following command. don’t forget to save the mnemonic
zenrockd keys add $WALLET

# to restore exexuting wallet, use the following command
zenrockd keys add $WALLET --recover

# save wallet and validator address
WALLET_ADDRESS=$(zenrockd keys show $WALLET -a)
VALOPER_ADDRESS=$(zenrockd keys show $WALLET --bech val -a)
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile

# check sync status, once your node is fully synced, the output from above will print "false"
zenrockd status 2>&1 | jq 

# before creating a validator, you need to fund your wallet and check balance
zenrockd query bank balances $WALLET_ADDRESS 
```

## Claim token on faucet <a href="#node-sync-status" id="node-sync-status"></a>

[https://gardia.zenrocklabs.io/workspaces](https://gardia.zenrocklabs.io/workspaces)

## Or use this command

```bash
curl https://faucet.gardia.zenrocklabs.io -XPOST -d'{"address":"zen1steffht....teqf"}'
```

### Node Sync Status Checker <a href="#node-sync-status" id="node-sync-status"></a>

```bash
#!/bin/bash
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.zrchain/config/config.toml" | cut -d ':' -f 3)
while true; do
  local_height=$(curl -s localhost:$rpc_port/status | jq -r '.result.sync_info.latest_block_height')
  network_height=$(curl -s https://zenrock-testnet-rpc.itrocket.net/status | jq -r '.result.sync_info.latest_block_height')

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
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(zenrockd comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000urock\",
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
zenrockd tx validation create-validator /root/validator.json \
    --node https://rpc.gardia.zenrocklabs.io \
    --gas-prices 30urock \
    --from $Wallet \
    --chain-id gardia-5
	
```

### Delegate to your node <a href="#delete" id="delete"></a>

```bash
zenrockd tx validation delegate $(zenrockd keys show $Wallet --bech val -a) 1000000urock --from $Wallet --chain-id gardia-4 --fees 30urock -y 
```

### Delete node <a href="#delete" id="delete"></a>

```bash
sudo systemctl stop zenrockd
sudo systemctl disable zenrockd
sudo rm -rf /etc/systemd/system/zenrockd.service
sudo rm $(which zenrockd)
sudo rm -rf $HOME/.zrchain
sed -i "/ZENROCK_/d" $HOME/.bash_profile
```
