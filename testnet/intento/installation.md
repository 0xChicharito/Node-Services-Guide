# ⚙️ Installation

```bash
MONIKER=Chicharito
echo "export MONIKER=$MONIKER" >> $HOME/.bash_profile
echo "export INTENTO_CHAIN_ID="intento-ics-test-1"" >> $HOME/.bash_profile
echo "export INTENTO_PORT="17"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
git clone https://github.com/trstlabs/intento.git
cd intento
git checkout v0.9.2-r1
make install
```

```bash
intentod config node tcp://localhost:${INTENTO_PORT}657
intentod config keyring-backend os
intentod config chain-id intento-ics-test-1
intentod init "Chicharito" --chain-id intento-ics-test-1
```

```bash
curl -o $HOME/.intento/config/genesis.json https://raw.githubusercontent.com/trstlabs/networks/main/testnet/intento-ics-test-1/genesis.json
```

```bash
SEEDS=""
PEERS="e4716092ee96fe0162e25c5cbabe74d6406ff304@65.108.127.249:7900"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.intento/config/config.toml
```

```bash
# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${PELL_PORT}317%g;
s%:8080%:${INTENTO_PORT}080%g;
s%:9090%:${INTENTO_PORT}090%g;
s%:9091%:${INTENTO_PORT}091%g;
s%:8545%:${INTENTO_PORT}545%g;
s%:8546%:${INTENTO_PORT}546%g;
s%:6065%:${INTENTO_PORT}065%g" $HOME/.intento/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${PELL_PORT}658%g;
s%:26657%:${INTENTO_PORT}657%g;
s%:6060%:${INTENTO_PORT}060%g;
s%:26656%:${INTENTO_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${INTENTO_PORT}656\"%;
s%:26660%:${INTENTO_PORT}660%g" $HOME/.intento/config/config.toml

# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.intento/config/app.toml 
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.intento/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"19\"/" $HOME/.intento/config/app.toml


sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.001uinto,0.001uatom"|g' $HOME/.intento/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.intento/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.intento/config/config.toml
sed -i -E "s|localhost|0.0.0.0|g" $HOME/.intento/config/app.toml
sed -i -E 's|unsafe-cors = .*|unsafe-cors = true|g' $HOME/.intento/config/app.toml
```

```bash
intentod keys add Chicharito
```

```bash
git clone https://github.com/cosmos/gaia.git
cd gaia
git checkout v22.1.0
make install # or make build, in case of issues you may try also try LDFLAGS="" make install
```

```bash
gaiad init "test" --chain-id GAIA
```

```bash
gaiad keys add Chicharito 
```

```bash
cd $HOME/.gaia
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(gaiad comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000uatom\",
    \"moniker\": \"Chicharito | Node9X\",
    \"identity\": \"9CAAFA62F3D9D33B\",
    \"website\": \"https://node9x.com/\",
    \"security\": \"contact@node9x.com\",
    \"details\": \"I love Blockchain ❤️ | Telegram: @Oxchicharito | Discord: 0xchicharito | Twitter: @0xchicharito | Email: contact@node9x.com\",
    \"commission-rate\": \"0.1\",
    \"commission-max-rate\": \"0.2\",
    \"commission-max-change-rate\": \"0.01\",
    \"min-self-delegation\": \"1\"
}" > validator.json

gaiad tx staking create-validator $HOME/.gaia/validator.json \
    --from Chicharito \
    --chain-id GAIA \
    --gas-adjustment 2 \
    --fees=855uatom \
    --gas auto \
    --node https://provider-test-rpc.intento.zone/
    -y

```

```bash
gaiad tx provider opt-in 4 --from Chicharito --chain-id GAIA --fees 5000uatom --gas auto --gas-adjustment=1.2 --node https://provider-test-rpc.intento.zone/
```

```bash
sudo tee /etc/systemd/system/intentod.service > /dev/null <<EOF
[Unit]
Description=Intento Testnet Node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.intento
ExecStart=$(which intentod) start --home $HOME/.intento
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF


gaiad tx staking delegate $(gaiad keys show Chicharito --bech val -a) 1000000uatom --from Chicharito --chain-id GAIA --fees 500uatom --gas auto --gas-adjustment 1.5 --node https://provider-test-rpc.intento.zone/ -y 
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable intentod
sudo systemctl start intentod && sudo journalctl -u intentod -f
sudo systemctl restart intentod && sudo journalctl -u intentod -f


sudo systemctl status intentod

sudo systemctl stop intentod
```

```bash
cd $HOME/.intento
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(intentod comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000uinto\",
    \"moniker\": \"Chicharito | Node9X\",
    \"identity\": \"9CAAFA62F3D9D33B\",
    \"website\": \"https://node9x.com/\",
    \"security\": \"contact@node9x.com\",
    \"details\": \"I love Blockchain ❤️ | Telegram: @Oxchicharito | Discord: 0xchicharito | Twitter: @0xchicharito | Email: contact@node9x.com\",
    \"commission-rate\": \"0.1\",
    \"commission-max-rate\": \"0.2\",
    \"commission-max-change-rate\": \"0.01\",
    \"min-self-delegation\": \"1\"
}" > validator.json

intentod tx staking create-validator $HOME/.intento/validator.json \
    --from Chicharito \
    --chain-id intento-ics-test-1 \
    --fees=500uinto \
    --gas auto \
    --gas-adjustment 2 \
    -y

```





