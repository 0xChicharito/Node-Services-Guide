# 🛰️ Command

## **Add new key**

```
zenrockd keys add wallet
```

## **Recover existing key**

```
zenrockd keys add wallet --recover
```

## **List all keys**

```
zenrockd keys list
```

## **Delete key**

```
zenrockd keys delete wallet
```

## **Export key to a file**

```
zenrockd keys export wallet
```

## **Import key from a file**

```
zenrockd keys import wallet wallet.backup
```

## **Query wallet balance**

```
zenrockd q bank balances $(zenrockd keys show wallet -a)
```

## Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

## **Create new validator**

```
zenrockd tx validation create-validator <(cat <<EOF
{
  "pubkey": $(zenrockd comet show-validator),
  "amount": "1000000urock",
  "moniker": "YOUR_MONIKER_NAME",
  "identity": "YOUR_KEYBASE_ID",
  "website": "YOUR_WEBSITE_URL",
  "security": "YOUR_SECURITY_EMAIL",
  "details": "YOUR_DETAILS",
  "commission-rate": "0.05",
  "commission-max-rate": "0.20",
  "commission-max-change-rate": "0.05",
  "min-self-delegation": "1"
}
EOF
) \
--chain-id gardia-2 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0urock \
-y
```

## **Edit existing validator**

```
zenrockd tx validation edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id gardia-2 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0urock \
-y
```

## **Unjail validator**

```
zenrockd tx slashing unjail --from wallet --chain-id gardia-2 --gas-adjustment 1.4 --gas auto --gas-prices 0urock -y
```

## **Jail reason**

```
zenrockd query slashing signing-info $(zenrockd comet show-validator)
```

## **List all active validators**

```
zenrockd q validation validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## **List all inactive validators**

```
zenrockd q validation validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## **View validator details**

```
zenrockd q validation validator $(zenrockd keys show wallet --bech val -a)
```

## Token management <a href="#token-management" id="token-management"></a>

## **Withdraw rewards from all validators**

```
zenrockd tx distribution withdraw-all-rewards --from wallet --chain-id gardia-2 --gas-adjustment 1.4 --gas auto --gas-prices 0urock -y
```

## **Withdraw commission and rewards from your validator**

```
zenrockd tx distribution withdraw-rewards $(zenrockd keys show wallet --bech val -a) --commission --from wallet --chain-id gardia-2 --gas-adjustment 1.4 --gas auto --gas-prices 0urock -y
```

## **Delegate tokens to yourself**

```
zenrockd tx validation delegate $(zenrockd keys show wallet --bech val -a) 1000000urock --from wallet --chain-id gardia-2 --gas-adjustment 1.4 --gas auto --gas-prices 0urock -y
```

## **Delegate tokens to validator**

```
zenrockd tx validation delegate <TO_VALOPER_ADDRESS> 1000000urock --from wallet --chain-id gardia-2 --gas-adjustment 1.4 --gas auto --gas-prices 0urock -y
```

## **Redelegate tokens to another validator**

```
zenrockd tx validation redelegate $(zenrockd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000urock --from wallet --chain-id gardia-2 --gas-adjustment 1.4 --gas auto --gas-prices 0urock -y
```

## **Unbond tokens from your validator**

```
zenrockd tx validation unbond $(zenrockd keys show wallet --bech val -a) 1000000urock --from wallet --chain-id gardia-2 --gas-adjustment 1.4 --gas auto --gas-prices 0urock -y
```

## **Send tokens to the wallet**

```
zenrockd tx bank send wallet <TO_WALLET_ADDRESS> 1000000urock --from wallet --chain-id gardia-2 --gas-adjustment 1.4 --gas auto --gas-prices 0urock -y
```

## Governance <a href="#governance" id="governance"></a>

## **List all proposals**

```
zenrockd query gov proposals
```

## **View proposal by id**

```
zenrockd query gov proposal 1
```

## **Vote ‘Yes’**

```
zenrockd tx gov vote 1 yes --from wallet --chain-id gardia-2 --gas-adjustment 1.4 --gas auto --gas-prices 0urock -y
```

## **Vote ‘No’**

```
zenrockd tx gov vote 1 no --from wallet --chain-id gardia-2 --gas-adjustment 1.4 --gas auto --gas-prices 0urock -y
```

## **Vote ‘Abstain’**

```
zenrockd tx gov vote 1 abstain --from wallet --chain-id gardia-2 --gas-adjustment 1.4 --gas auto --gas-prices 0urock -y
```

## **Vote ‘NoWithVeto’**

```
zenrockd tx gov vote 1 NoWithVeto --from wallet --chain-id gardia-2 --gas-adjustment 1.4 --gas auto --gas-prices 0urock -y
```



## Maintenance <a href="#maintenance" id="maintenance"></a>

## **Get validator info**

```
zenrockd status 2>&1 | jq .ValidatorInfo
```

## **Get sync info**

```
zenrockd status 2>&1 | jq .SyncInfo
```

## **Get node peer**

```
echo $(zenrockd comet show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.zrchain/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

## **Check if validator key is correct**

```
[[ $(zenrockd q validation validator $(zenrockd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(zenrockd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

## **Enable prometheus**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.zrchain/config/config.toml
```

## **Reset chain data**

```
zenrockd comet unsafe-reset-all --keep-addr-book --home $HOME/.zrchain --keep-addr-book
```

## **Remove node**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop zenrock-testnet.service
sudo systemctl disable zenrock-testnet.service
sudo rm /etc/systemd/system/zenrock-testnet.service
sudo systemctl daemon-reload
rm -f $(which zenrockd)
rm -rf $HOME/.zrchain
```

## Service Management <a href="#service-management" id="service-management"></a>

## **Reload service configuration**

```
sudo systemctl daemon-reload
```

## **Enable service**

```
sudo systemctl enable zenrock-testnet.service
```

## **Disable service**

```
sudo systemctl disable zenrock-testnet.service
```

## **Start service**

```
sudo systemctl start zenrock-testnet.service
```

## **Stop service**

```
sudo systemctl stop zenrock-testnet.service
```

## **Restart service**

```
sudo systemctl restart zenrock-testnet.service
```

## **Check service status**

```
sudo systemctl status zenrock-testnet.service
```

## **Check service logs**

```
sudo journalctl -u zenrock-testnet.service -f --no-hostname -o cat
```
