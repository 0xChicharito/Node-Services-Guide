# Useful commands



```autohotkey
# Set Vars
MONIKER=<YOUR_MONIKER_NAME_GOES_HERE>
echo "export MONIKER=$MONIKER" >> $HOME/.bash_profile
echo "export ALLORA_CHAIN_ID="testnet"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

\
**WALLET MANAGEMENT**

{% hint style="info" %}
_Add Wallet Specify the value \<wallet> with your own wallet name_
{% endhint %}

```autohotkey
#Create Wallet
allorad keys add wallet

#Recover Wallet
allorad keys add wallet --recover

#List Wallet
allorad keys list

#Delete Wallet
allorad keys delete wallet

#Check Wallet Balance
allorad q bank balances $(allorad keys show wallet -a)
```

**VALIDATOR MANAGEMENT**

{% hint style="info" %}
_Please adjust , MONIKER , YOUR\_KEYBASE\_ID , YOUR\_DETAILS , YOUR\_WEBSITE\_URL_
{% endhint %}

```autohotkey
#Check Pubkey
allorad tendermint show-validator

#Make file validator.json
tee $HOME/.allorad/validator.json > /dev/null << EOF
{
    "pubkey": YOUR_PUBKEY,
    "amount": "1000000000uallo",
    "moniker": "MONIKER",
    "identity": "YOUR_KEYBASE_ID",
    "website": "YOUR_WEBSITE_URL",
    "details": "YOUR_DETAILS.",
    "commission-rate": "0.1",
    "commission-max-rate": "0.2",
    "commission-max-change-rate": "0.01",
    "min-self-delegation": "1"
}
EOF

#Create Validator
allorad --home $HOME/.allorad tx staking create-validator $HOME/.allorad/validator.json --from wallet --chain-id $ALLORA_CHAIN_ID --gas 220000 --gas-prices 1uallo

#Edit Validator
allorad tx staking edit-validator \
--new-moniker="YOUR MONIKER" \
--identity="IDENTITY KEYBASE" \
--details="DETAILS VALIDATOR" \
--website="LINK WEBSITE" \
--chain-id=$ALLORA_CHAIN_ID \
--from=wallet \
--gas-adjustment=1.5 \
--gas="auto" \
--gas-prices=1uallo \
-y

#Unjail Validator
allorad tx slashing unjail --from wallet --chain-id $ALLORA_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 1uallo -y

#Check Jailed Reason
allorad query slashing signing-info $(allorad tendermint show-validator)
```

**TOKEN MANAGEMENT**

{% code fullWidth="false" %}
```autohotkey
#Withdraw Rewards
allorad tx distribution withdraw-all-rewards --from wallet --chain-id $ALLORA_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 1uallo -y

#Withdraw Rewards with Comission
allorad tx distribution withdraw-rewards $(allorad keys show wallet --bech val -a) --commission --from wallet --chain-id $ALLORA_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 1uallo -y

#Delegate Token to your own validator
allorad tx staking delegate $(allorad keys show wallet --bech val -a) 100000000uallo --from wallet --chain-id $ALLORA_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 1uallo -y

#Delegate Token to other validator
allorad tx staking redelegate $(allorad keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uallo --from wallet --chain-id $ALLORA_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 1uallo -y

#Unbond Token from your validator
allorad tx staking unbond $(allorad keys show wallet --bech val -a) 1000000000uallo --from wallet --chain-id $ALLORA_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 1uallo -y

#Send Token to another wallet
allorad tx bank send wallet <TO_WALLET_ADDRESS> 100000000uallo --from wallet --chain-id $ALLORA_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 1uallo -y
```
{% endcode %}

**GOVERNANCE**

```autohotkey
#Vote, You can change the value of yes to no,abstain,nowithveto

allorad tx gov vote 1 yes --from wallet --chain-id $ALLORA_CHAIN_ID --gas-adjustment 1.5 --gas auto --gas-prices 1uallo -y

#List all proposals
allorad query gov proposals

#Create new text proposal
allorad tx gov submit-proposal \
--title="Title" \
--description="Description" \
--deposit=1000000000uallo \
--type="Text" \
--from=wallet \
--gas-prices=1uallo\ 
--gas-adjustment=1.5 \
--gas="auto" \
-y 
```

**SERVICES MANAGEMENT**

```autohotkey
# Reload Service
sudo systemctl daemon-reload

# Enable Service
sudo systemctl enable allorad

# Disable Service
sudo systemctl disable allorad

# Start Service
sudo systemctl start allorad

# Stop Service
sudo systemctl stop allorad

# Restart Service
sudo systemctl restart allorad

# Check Service Status
sudo systemctl status allorad

# Check Service Logs
sudo journalctl -u allorad -f --no-hostname -o cat
```

**UTILITY**

```autohotkey
#Set Indexer NULL / KV
sed -i 's|^indexer *=.*|indexer = "null"|' $HOME/.allorad/config/config.toml

#Get Validator info
allorad status 2>&1 | jq .ValidatorInfo

#Get denom info
allorad q bank denom-metadata -oj | jq

#Get sync status
allorad status 2>&1 | jq .sync_info.catching_up

#Get latest height
allorad status 2>&1 | jq .sync_info.latest_block_height

#Reset Node
allorad tendermint unsafe-reset-all --home $HOME/.allorad --keep-addr-book
```

**DELETE NODE**

```autohotkey
sudo systemctl stop allorad && sudo systemctl disable allorad && sudo rm /etc/systemd/system/allorad.service && sudo systemctl daemon-reload && sudo rm -rf $(which allorad) && rm -rf $HOME/.allorad && rm -rf $HOME/go/bin/allorad
```
