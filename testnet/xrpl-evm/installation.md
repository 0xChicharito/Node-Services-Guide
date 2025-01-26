# ⚙️ Installation

```bash
# install go, if needed
cd $HOME
VER="1.21.3"
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
echo "export XRPL_CHAIN_ID="exrp_1440002-1"" >> $HOME/.bash_profile
echo "export XRPL_PORT="13"" >> $HOME/.bash_profile
source $HOME/.bash_profile

# download binary
cd $HOME
rm -rf xrp
git clone https://github.com/xrplevm/node xrp
cd xrp
git checkout v6.0.0
make install

# config and init app
exrpd config node tcp://localhost:${XRPL_PORT}657
exrpd config keyring-backend os
exrpd config chain-id exrp_1440002-1
exrpd init "test" --chain-id exrp_1440002-1

# download genesis and addrbook
wget -O $HOME/.exrpd/config/genesis.json https://server-2.itrocket.net/testnet/xrpl/genesis.json
wget -O $HOME/.exrpd/config/addrbook.json  https://server-2.itrocket.net/testnet/xrpl/addrbook.json

# set seeds and peers
SEEDS="6a271a9b7d07393a1521b1c7386a9fa9147a1762@xrpl-testnet-seed.itrocket.net:16656"
PEERS="166f7e48e1c756cc663fee5be96ab2bbdb4db970@xrpl-testnet-peer.itrocket.net:13656,d2f2a052eb0106cf03d2189482983efcc372a92a@116.202.174.189:26656,5c46af079f2acfb54f3805f302fd94a7089cfc03@31.220.87.80:26656,45ce5323085fa1a05771f1c10ac22976e44e3ef0@195.154.91.82:26656,e60a252c148b17122107eba50b4d1b16b7100fa7@23.129.20.120:30056,6a66cdc4b234f57fc5357c9584b20c4149b63efe@52.205.83.216:26656"
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
if curl -s --head curl https://server-2.itrocket.net/testnet/xrpl/xrpl_2025-01-26_14131007_snap.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://server-2.itrocket.net/testnet/xrpl/xrpl_2025-01-26_14131007_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.exrpd
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
