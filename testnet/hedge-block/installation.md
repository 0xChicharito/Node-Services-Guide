---
description: 'Recommended Hardware: 8 Cores, 64GB RAM, 500GB of storage (NVME)'
---

# Installation

#### Installation <a href="#installation" id="installation"></a>

Install dependencies, if needed

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git jq build-essential gcc unzip wget lz4 -y
sudo apt-get install wget liblz4-tool aria2 -y
```

Install go, if needed

```
cd $HOME && \
ver="1.22.0" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile && \
source ~/.bash_profile && \
go version
```

Dowload ibwasmvm.x86\_64.so

```
  wget -O /lib/libwasmvm.x86_64.so https://github.com/CosmWasm/wasmvm/releases/download/v1.3.0/libwasmvm.x86_64.so
```

Set vars

```bash
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="Chicharito"" >> $HOME/.bash_profile
echo "export HEDGE_CHAIN_ID="berberis-1"" >> $HOME/.bash_profile
echo "export HEDGE_PORT="10"" >> $HOME/.bash_profile
source $HOME/.bash_profile

```

Dowload Hedged

```
mkdir -p $HOME/go/bin
sudo wget -O hedged https://github.com/hedgeblock/testnets/releases/download/v0.1.0/hedged_linux_amd64_v0.1.0
chmod +x hedged
sudo mv hedged $HOME/go/bin
```

Config and init app

```
hedged init $MONIKER --chain-id $HEDGE_CHAIN_ID 
sed -i -e "s|^node *=.*|node = \"tcp://localhost:${HEDGE_PORT}657\"|" $HOME/.hedge/config/client.toml
```

Download genesis and addrbook

```
sudo wget -O $HOME/.hedge/config/genesis.json "https://raw.githubusercontent.com/dongqn/GuideNode/main/Hedge/genesis.json"
sudo wget -O $HOME/.hedge/config/addrbook.json "https://raw.githubusercontent.com/dongqn/GuideNode/main/Hedge/addrbook.json"
```

```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025uhedge\"/;" ~/.hedge/config/app.toml
peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.hedge/config/config.toml
seeds="7879005ab63c009743f4d8d220abd05b64cfee3d@54.92.167.150:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.hedge/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.hedge/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.hedge/config/config.toml
```

Set custom ports in app.toml

```bash
sed -i.bak -e "s%:1317%:${HEDGE_PORT}317%g;
s%:8080%:${HEDGE_PORT}080%g;
s%:9090%:${HEDGE_PORT}090%g;
s%:9091%:${HEDGE_PORT}091%g;
s%:8545%:${HEDGE_PORT}545%g;
s%:8546%:${HEDGE_PORT}546%g;
s%:6065%:${HEDGE_PORT}065%g" $HOME/.hedge/config/app.toml
```

Set custom ports in config.toml file

```bash
sed -i.bak -e "s%:26658%:${AIRCHAIN_PORT}658%g;
s%:26657%:${HEDGE_PORT}657%g;
s%:6060%:${HEDGE_PORT}060%g;
s%:26656%:${HEDGE_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${HEDGE_PORT}656\"%;
s%:26660%:${HEDGE_PORT}660%g" $HOME/.hedge/config/config.toml
```



Pruning and indexer

```
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="10" && \
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.hedge/config/app.toml && \
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.hedge/config/app.toml && \
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.hedge/config/app.toml && \
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.hedge/config/app.toml
```

Create hedged service for your node to run in the background

```
sudo tee /etc/systemd/system/hedged.service > /dev/null <<EOF
[Unit]
Description=Hedged Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which hedged) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

Start node

```
sudo systemctl daemon-reload && \
sudo systemctl enable hedged && \
sudo systemctl restart hedged && \
sudo journalctl -u hedged -f -o cat
```

**Check for your syncing progress**

```
hedged status 2>&1 | jq
```

### Create wallet <a href="#create-wallet" id="create-wallet"></a>

```
hedged keys add wallet

# DO NO FORGET TO SAVE THE SEED PHRASE & YOUR PASSPHRASE YOU SET FOR THIS WALLET
# You can add --recover flag to restore existing key instead of creating
```

Check Balance

```
hedged q bank balances $(hedged keys show wallet -a)
```

### **Create validator** <a href="#create-validator" id="create-validator"></a>

```
hedged tx staking create-validator \
--amount=1000000uhedge \
--pubkey=$(hedged tendermint show-validator) \
--moniker="Moniker" \
--chain-id=berberis-1 \
--commission-rate=0.10 \
--commission-max-rate=0.20 \
--commission-max-change-rate=0.1 \
--min-self-delegation=1 --from=wallet \
--gas-prices=0.025uhedge \
--gas-adjustment=1.5 \
--gas=auto \
-y
```

Delegate to your self

```
hedged tx staking delegate $(hedged keys show wallet --bech val -a) 1000000uhedge --from wallet --chain-id berberis-1 --gas-prices=0.025uhedge --gas-adjustment=1.5 --gas=auto -y
```

Unjail

```
hedged tx slashing unjail --from wallet --chain-id=berberis-1 --gas-prices=0.025uhedge --gas-adjustment=1.5 --gas=auto -y 
```

Get Validator Info

```
hedged status 2>&1 | jq -r '.ValidatorInfo // .validator_info'
```

#### Node Snapshot Testnet Hedgeblock <a href="#node-snapshot-testnet-hedgeblock" id="node-snapshot-testnet-hedgeblock"></a>

Install requirement if don't have:

```bash
sudo apt install lz4 -y
```

Stop node

```bash
sudo systemctl stop hedged
```

Back up priv\_validator\_state.json

```bash
cp $HOME/.hedge/data/priv_validator_state.json $HOME/.hedge/priv_validator_state.json.bak
```

Reset chain data

```bash
hedged tendermint unsafe-reset-all --home $HOME/.hedge --keep-addr-book
```

Download Snapshot Data

```bash
aria2c -x 16 -s 16 http://node39.top/testnet/hedge/snapshort-hedge-2762845.tar.lz4
lz4 -c -d snapshort-hedge-2762845.tar.lz4 | tar -x -C $HOME/.hedge/
```

Validator node move priv\_validator\_state.json that was backed up earlier

```bash
mv $HOME/.hedge/priv_validator_state.json.bak $HOME/.hedge/data/priv_validator_state.json
```

Restart node

```bash
sudo systemctl restart hedged
journalctl -fu hedged -o cat
```

Block check

```bash
while true; do
local_height=$(curl -s localhost:10657/status | jq -r .result.sync_info.latest_block_height); network_height=$(curl -s https://testnet-hedge-rpc.konsortech.xyz/status | jq -r .result.sync_info.latest_block_height); blocks_left=$((network_height - local_height)); echo "Your node height: $local_height"; echo "Network height: $network_height"; echo "Blocks left: $blocks_left"
 sleep 5
done
```

### Congratulation!! <a href="#congratulation" id="congratulation"></a>

Now you have completed your node for 0gchain and we will move on to creating your storage node next.
