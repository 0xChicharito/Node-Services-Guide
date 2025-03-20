# 🕹️ Command

```bash
# Set Vars
MONIKER=<YOUR_MONIKER_NAME_GOES_HERE>
echo "export MONIKER=$MONIKER" >> $HOME/.bash_profile
echo "export STRUCTS_CHAIN_ID="structstestnet-100"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

#### WALLET MANAGEMENT <a href="#wallet-management" id="wallet-management"></a>

_Add Wallet Specify the value \<wallet> with your own wallet name_

```bash
#Create Wallet
structsd keys add wallet

#Recover Wallet
structsd keys add wallet --recover

#List Wallet
structsd keys list

#Delete Wallet
structsd keys delete wallet

#Check Wallet Balance
structsd q bank balances $WALLET_ADDRESS --node tcp://0.0.0.0:26657
```

#### VALIDATOR MANAGEMENT <a href="#validator-management" id="validator-management"></a>

_Please adjust , MONIKER , YOUR\_KEYBASE\_ID , YOUR\_DETAILS , YOUR\_WEBSITE\_URL_

```bash
#Create Validator
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(structsd comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1ualpha\",
    \"moniker\": \"$MONIKER\",
    \"identity\": \"9CAAFA62F3D9D33B\",
    \"website\": \"https://node9x.com/",
    \"security\": \"contact@node9x.com\",
    \"details\": \"I love Structs ❤️\",
    \"commission-rate\": \"0.1\",
    \"commission-max-rate\": \"0.2\",
    \"commission-max-change-rate\": \"0.01\",
    \"min-self-delegation\": \"1\"
}" > validator.json
# Create a validator using the JSON configuration
structsd tx staking create-validator $HOME/validator.json \
  --from wallet \
  --chain-id structstestnet-101 \
  --node tcp://0.0.0.0:26657  \
  --gas auto \
  -y

#Edit Validator
structsd tx staking edit-validator \
--new-moniker="YOUR MONIKER" \
--identity="IDENTITY KEYBASE" \
--details="DETAILS VALIDATOR" \
--website="LINK WEBSITE" \
--chain-id=$STRUCTS_CHAIN_ID \
--from=wallet \
--gas-adjustment=1.5 \
--gas="auto" \
--gas-prices=0.1ualpha \
-y

#Unjail Validator
structsd tx slashing unjail --from wallet --chain-id $STRUCTS_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 0.1ualpha --node tcp://0.0.0.0:26657 -y

#Check Jailed Reason
structsd query slashing signing-info $(structsd tendermint show-validator)
```

#### TOKEN MANAGEMENT <a href="#token-management" id="token-management"></a>

```bash
#Withdraw Rewards
structsd tx distribution withdraw-all-rewards --from wallet --chain-id $STRUCTS_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 0.1alpha -y

#Withdraw Rewards with Comission
structsd tx distribution withdraw-rewards $(structsd keys show wallet --bech val -a) --commission --from wallet --chain-id $STRUCTS_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 0.1ualpha -y

#Delegate Token to your own validator
structsd tx staking delegate $(structsd keys show wallet --bech val -a) 1000000ualpha --from wallet --chain-id $STRUCTS_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 0.1ualpha -y

#Delegate Token to other validator
structsd tx staking redelegate $(structsd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000ualpha --from wallet --chain-id $STRUCTS_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 0.1ualpha -y

#Unbond Token from your validator
structsd tx staking unbond $(structsd keys show wallet --bech val -a) 1000000ualpha --from wallet --chain-id $STRUCTS_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 0.1ualpha -y

#Send Token to another wallet
structsd tx bank send wallet <TO_WALLET_ADDRESS> 1000000ualpha --from wallet --chain-id $STRUCTS_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 0.1ualpha -y
```

#### GOVERNANCE <a href="#governance" id="governance"></a>

```bash
#Vote You can change the value of yes to no,abstain,nowithveto
structsd tx gov vote 1 yes --from wallet --chain-id $STRUCTS_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 10ualpha -y

#List all proposals
structsd query gov proposals

#Create new text proposal
structsd tx gov submit-proposal \
--title="Title" \
--description="Description" \
--deposit=10000000ualpha \
--type="Text" \
--from=wallet \
--gas-prices=10ualpha \ 
--gas-adjustment=1.5 \
--gas="auto" \
-y 
```

#### SERVICES MANAGEMENT <a href="#services-management" id="services-management"></a>

```bash
# Reload Service
sudo systemctl daemon-reload

# Enable Service
sudo systemctl enable structsd

# Disable Service
sudo systemctl disable structsd

# Start Service
sudo systemctl start structsd

# Stop Service
sudo systemctl stop structsd

# Restart Service
sudo systemctl restart structsd

# Check Service Status
sudo systemctl status structsd

# Check Service Logs
sudo journalctl -u structsd -f --no-hostname -o cat
```

#### UTILITY <a href="#utility" id="utility"></a>

```bash
#Set Indexer NULL / KV
sed -i 's|^indexer *=.*|indexer = "null"|' $HOME/.structs/config/config.toml

#Get Validator info
structsd status 2>&1 | jq .ValidatorInfo

#Get denom info
structsd q bank denom-metadata -oj | jq

#Get sync status
structsd status 2>&1 | jq .SyncInfo.catching_up

#Get latest height
structsd status 2>&1 | jq .SyncInfo.latest_block_height

#Reset Node
structsd tendermint unsafe-reset-all --home $HOME/.structs --keep-addr-book
```

#### DELETE NODE <a href="#delete-node" id="delete-node"></a>

**WARNING! Use this command wisely Backup your key first it will remove node from your system**

```bash
sudo systemctl stop structsd && sudo systemctl disable structsd && sudo rm /etc/systemd/system/structsd.service && sudo systemctl daemon-reload && sudo rm -rf $(which structsd) && rm -rf $HOME/.structs && rm -rf $HOME/structsd
```
