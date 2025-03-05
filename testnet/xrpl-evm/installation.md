# ⚙️ Installation

```bash
# install go, if needed
cd $HOME
VER="1.23.4"
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
echo "export XRPL_CHAIN_ID="xrplevm_1449000-1"" >> $HOME/.bash_profile
echo "export XRPL_PORT="13"" >> $HOME/.bash_profile
source $HOME/.bash_profile

# download binary
cd $HOME
rm -rf xrp
git clone https://github.com/xrplevm/node xrp
cd xrp
git checkout v6.0.0
make install

#Install WASMVM
export WASMVM_VERSION=v2.1.2
export LD_LIBRARY_PATH=$HOME/.exrpd/lib
mkdir -p $LD_LIBRARY_PATH
wget "https://github.com/CosmWasm/wasmvm/releases/download/$WASMVM_VERSION/libwasmvm.$(uname -m).so" -O "$LD_LIBRARY_PATH/libwasmvm.$(uname -m).so"
echo "export LD_LIBRARY_PATH=$HOME/.exrpd/lib:$LD_LIBRARY_PATH" >> ~/.bashrc
source ~/.bashrc

# config and init app
exrpd config node tcp://localhost:${XRPL_PORT}657
exrpd config keyring-backend os
exrpd config chain-id xrplevm_1449000-1
exrpd init "test" --chain-id xrplevm_1449000-1

# download genesis and addrbook
wget -O $HOME/.exrpd/config/genesis.json https://snapshots.polkachu.com/testnet-genesis/xrp/genesis.json
wget -O $HOME/.exrpd/config/addrbook.json https://snapshots.polkachu.com/testnet-addrbook/xrp/addrbook.json
# set seeds and peers
SEEDS=""
PEERS=972f58b459debdbaa92fd8479d89128b653d7eb8@65.21.29.250:3640,f8452f28064e9cf9ef1df0c055ac0280576143b6@65.108.69.56:26696,b96d3e221688108e40706d51cca59d80a60f67e9@65.21.200.7:3640,1881f3f71603b7eba91b8b84148834c7322122be@45.77.195.1:26656,6c5b34685a0c1956bde097914e42bc537f5ca5c7@79.137.70.143:26646
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.exrpd/config/config.toml

sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.exrpd/config/config.toml

# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${XRPL_PORT}317%g;
s%:8080%:${XRPL_PORT}080%g;
s%:9090%:${XRPL_PORT}090%g;
s%:9091%:${XRPL_PORT}091%g;
s%:8545%:${XRPL_PORT}545%g;
s%:8546%:${XRPL_PORT}546%g;
s%:6065%:${XRPL_PORT}065%g" $HOME/.exrpd/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${XRPL_PORT}658%g;
s%:26657%:${XRPL_PORT}657%g;
s%:6060%:${XRPL_PORT}060%g;
s%:26656%:${XRPL_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${XRPL_PORT}656\"%;
s%:26660%:${XRPL_PORT}660%g" $HOME/.exrpd/config/config.toml

# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.exrpd/config/app.toml 
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.exrpd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"19\"/" $HOME/.exrpd/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0axrp"|g' $HOME/.exrpd/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.exrpd/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.exrpd/config/config.toml

# create service file
sudo tee /etc/systemd/system/exrpd.service > /dev/null <<EOF
[Unit]
Description=XRPL node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.exrpd
ExecStart=$(which exrpd) start --home $HOME/.exrpd
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# reset and download snapshot
exrpd tendermint unsafe-reset-all --home $HOME/.exrpd
if curl -s --head curl https://snapshots.polkachu.com/testnet-snapshots/xrp/xrp_243913.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://snapshots.polkachu.com/testnet-snapshots/xrp/xrp_243913.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.exrpd
    else
  echo "no snapshot found"
fi

# enable and start service
sudo systemctl daemon-reload
sudo systemctl enable exrpd
sudo systemctl restart exrpd && sudo journalctl -u exrpd -fo cat
```

### Create wallet <a href="#create-wallet" id="create-wallet"></a>

```bash
# to create a new wallet, use the following command. don’t forget to save the mnemonic
exrpd keys add $WALLET

# to restore exexuting wallet, use the following command
exrpd keys add $WALLET --recover

# save wallet and validator address
WALLET_ADDRESS=$(exrpd keys show $WALLET -a)
VALOPER_ADDRESS=$(exrpd keys show $WALLET --bech val -a)
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile

# check sync status, once your node is fully synced, the output from above will print "false"
exrpd status 2>&1 | jq 

# before creating a validator, you need to fund your wallet and check balance
exrpd query bank balances $WALLET_ADDRESS 
```

### Create validator <a href="#create-validator" id="create-validator"></a>

```bash
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(exrpd comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000uxrp\",
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
exrpd tx staking create-validator validator.json \
    --from $WALLET \
    --chain-id exrp_1440002-1 \
	--gas auto --gas-adjustment 1.5
	
```

### Delete node <a href="#delete" id="delete"></a>

```bash
sudo systemctl stop exrpd
sudo systemctl disable exrpd
sudo rm -rf /etc/systemd/system/exrpd.service
sudo rm $(which exrpd)
sudo rm -rf $HOME/.exrpd
sed -i "/XRPL_/d" $HOME/.bash_profile
```
