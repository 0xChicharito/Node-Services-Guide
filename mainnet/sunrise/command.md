# 🕹️ Command

### Wallet: <a href="#wallet" id="wallet"></a>

#### Add New Wallet: <a href="#add-new-wallet" id="add-new-wallet"></a>

```
sunrised keys add wallet
```

#### Restore executing wallet: <a href="#restore-executing-wallet" id="restore-executing-wallet"></a>

```
sunrised keys add wallet --recover
```

#### List All Wallets: <a href="#list-all-wallets" id="list-all-wallets"></a>

```
sunrised keys list
```

#### Delete wallet: <a href="#delete-wallet" id="delete-wallet"></a>

```
sunrised keys delete wallet
```

#### Check Balance: <a href="#check-balance" id="check-balance"></a>

```
sunrised q bank balances $(sunrised keys show wallet -a)
```

### Validator: <a href="#validator" id="validator"></a>

#### Create Validator: <a href="#create-validator" id="create-validator"></a>

```bash
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(sunrised comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"9000000000000uvrise\",
    \"moniker\": \"test\",
    \"identity\": \"\",
    \"website\": \"\",
    \"security\": \"\",
    \"details\": \"CoinHunters Community ❤️\",
    \"commission-rate\": \"0.1\",
    \"commission-max-rate\": \"0.2\",
    \"commission-max-change-rate\": \"0.01\",
    \"min-self-delegation\": \"1\"
}" > validator.json
# Create a validator using the JSON configuration
sunrised tx staking create-validator validator.json \
  --from wallet \
  --chain-id sunrise-1 \
  --gas auto --gas-adjustment 1.5 --fees 0.0uvrise \
  -y
```

**UNJAIL VALIDATOR**

```
sunrised tx slashing unjail --from wallet --chain-id sunrise-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uvrise -y
```

**JAIL REASON**

```
sunrised query slashing signing-info $(sunrised tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
sunrised q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
sunrised q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
sunrised q staking validator $(sunrised keys show wallet --bech val -a)
```

#### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
sunrised tx distribution withdraw-rewards $(sunrised keys show wallet --bech val -a) --commission --from wallet --chain-id sunrise-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uvrise -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
sunrised tx distribution withdraw-rewards $(sunrised keys show wallet --bech val -a) --commission --from wallet --chain-id sunrise-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uvrise -y
```

**DELEGATE TOKENS TO YOURSELF**

```
sunrised tx staking delegate $(sunrised keys show wallet --bech val -a) 1000000000000uvrise --from wallet --chain-id sunrise-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uvrise -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
sunrised tx staking delegate <TO_VALOPER_ADDRESS> 1000000000000uvrise --from wallet --chain-id sunrise-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uvrise -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```bash
sunrised tx staking redelegate $(sunrised keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000000000uvrise --from wallet --chain-id sunrise-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uvrise -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```bash
sunrised tx staking unbond $(sunrised keys show wallet --bech val -a) 1000000000000uvrise --from wallet --chain-id sunrise-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uvrise -y
```

**SEND TOKENS TO THE WALLET**

```
sunrised tx bank send wallet <TO_WALLET_ADDRESS> 1000000000000uvrise --from wallet --chain-id sunrise-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uvrise -y
```

#### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```bash
sunrised query gov proposals
```

**VIEW PROPOSAL BY ID**

```
sunrised query gov proposal 1
```

**VOTE ‘YES’**

```bash
sunrised tx gov vote 1 yes --from wallet --chain-id sunrise-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uvrise -y
```

**VOTE ‘NO’**

```bash
sunrised tx gov vote 1 no --from wallet --chain-id sunrise-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uvrise -y
```

**VOTE ‘ABSTAIN’**

```bash
sunrised tx gov vote 1 abstain --from wallet --chain-id sunrise-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uvrise -y
```

**VOTE ‘NOWITHVETO’**

```bash
sunrised tx gov vote 1 NoWithVeto --from wallet --chain-id sunrise-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uvrise -y
```

#### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```bash
UPDATE PORTS
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.sunrise/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.sunrise/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```bash
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.sunrise/config/config.toml
```

**Enable indexer**

```bash
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.sunrise/config/config.toml
```

**UPDATE PRUNING**

```bash
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.sunrise/config/app.toml
```

#### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```bash
sunrised status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```bash
sunrised status 2>&1 | jq
```

**GET NODE PEER**

```bash
echo $(sunrised tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.sunrise/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```bash
[[ $(sunrised q staking validator $(sunrised keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(sunrised status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```bash
curl -sS http://localhost:40657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```bash
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0uvrise\"/" $HOME/.sunrise/config/app.toml
```

**ENABLE PROMETHEUS**

```bash
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.sunrise/config/config.toml
```

**RESET CHAIN DATA**

```bash
sunrised tendermint unsafe-reset-all --keep-addr-book --home $HOME/.sunrise --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop sunrised
sudo systemctl disable sunrised
sudo rm /etc/systemd/system/sunrised.service
sudo systemctl daemon-reload
rm -f $(which sunrised)
rm -rf $HOME/.sunrise
rm -rf $HOME/sunrise
```

#### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable sunrised
```

**DISABLE SERVICE**

```
sudo systemctl disable sunrised
```

**START SERVICE**

```
sudo systemctl start sunrised
```

**STOP SERVICE**

```
sudo systemctl stop sunrised
```

**RESTART SERVICE**

```
sudo systemctl restart sunrised
```

**CHECK SERVICE STATUS**

```
sudo systemctl status sunrised
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u sunrised -f --no-hostname -o cat
```
