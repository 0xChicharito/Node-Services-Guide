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

```bash
zenrockd keys import wallet wallet.backup
```

## **Query wallet balance**

```bash
zenrockd q bank balances $(zenrockd keys show wallet -a)
```

## Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

## **Create new validator**

```bash
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
--chain-id gardia-4 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0urock \
-y
```

## **Edit existing validator**

```bash
zenrockd tx validation edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id gardia-4 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0urock \
-y
```

## **Unjail validator**

```bash
zenrockd tx slashing unjail --from wallet --chain-id gardia-4 --gas-prices 30urock -y
```

## **Jail reason**

```bash
zenrockd query slashing signing-info $(zenrockd comet show-validator)
```

## **List all active validators**

```bash
zenrockd q validation validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## **List all inactive validators**

```bash
zenrockd q validation validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## **View validator details**

```bash
zenrockd q validation validator $(zenrockd keys show wallet --bech val -a)
```

## Token management <a href="#token-management" id="token-management"></a>

## **Withdraw rewards from all validators**

```bash
zenrockd tx distribution withdraw-all-rewards --from wallet --chain-id gardia-2 --gas-prices 30urock -y
```

## **Withdraw commission and rewards from your validator**

```bash
zenrockd tx distribution withdraw-rewards $(zenrockd keys show wallet --bech val -a) --commission --from wallet --chain-id gardia-2 --gas-prices 30urock -y
```

## **Delegate tokens to yourself**

```bash
zenrockd tx validation delegate $(zenrockd keys show wallet --bech val -a) 1000000urock --from wallet --chain-id gardia-2 --gas-prices 30urock -y
```

## **Delegate tokens to validator**

```bash
zenrockd tx validation delegate <TO_VALOPER_ADDRESS> 1000000urock --from wallet --chain-id gardia-2 --gas-prices 30urock -y
```

## **Redelegate tokens to another validator**

```bash
zenrockd tx validation redelegate $(zenrockd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000urock --from wallet --chain-id gardia-2 -gas-prices 30urock -y
```

## **Unbond tokens from your validator**

```bash
zenrockd tx validation unbond $(zenrockd keys show wallet --bech val -a) 1000000urock --from wallet --chain-id gardia-2 --gas-prices 30urock -y
```

## **Send tokens to the wallet**

```bash
zenrockd tx bank send wallet <TO_WALLET_ADDRESS> 1000000urock --from wallet --chain-id gardia-2 --gas-prices 30urock -y
```

## Governance <a href="#governance" id="governance"></a>

## **List all proposals**

```bash
zenrockd query gov proposals
```

## **View proposal by id**

```bash
zenrockd query gov proposal 1
```

## **Vote ‘Yes’**

```bash
zenrockd tx gov vote 1 yes --from wallet --chain-id gardia-4 --gas-prices 500000urock -y
```

## **Vote ‘No’**

```bash
zenrockd tx gov vote 1 no --from wallet --chain-id gardia-4 --gas-prices 500000urock -y
```

## **Vote ‘Abstain’**

```bash
zenrockd tx gov vote 1 abstain --from wallet --chain-id gardia-4 --gas-prices 30urock -y
```

## **Vote ‘NoWithVeto’**

```bash
zenrockd tx gov vote 1 NoWithVeto --from wallet --chain-id gardia-2 --gas-prices 0urock -y
```



## Maintenance <a href="#maintenance" id="maintenance"></a>

## **Get validator info**

```bash
zenrockd status 2>&1 | jq .ValidatorInfo
```

## **Get sync info**

```bash
zenrockd status 2>&1 | jq .SyncInfo
```

## **Get node peer**

```bash
echo $(zenrockd comet show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.zrchain/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

## **Check if validator key is correct**

```bash
[[ $(zenrockd q validation validator $(zenrockd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(zenrockd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

## **Enable prometheus**

```bash
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.zrchain/config/config.toml
```

## **Reset chain data**

```bash
zenrockd comet unsafe-reset-all --keep-addr-book --home $HOME/.zrchain --keep-addr-book
```

## **Remove node**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```bash
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

```bash
sudo systemctl daemon-reload
```

## **Enable service**

```bash
sudo systemctl enable zenrock-testnet.service
```

## **Disable service**

```bash
sudo systemctl disable zenrock-testnet.service
```

## **Start service**

```bash
sudo systemctl start zenrock-testnet.service
```

## **Stop service**

```bash
sudo systemctl stop zenrock-testnet.service
```

## **Restart service**

```bash
sudo systemctl restart zenrock-testnet.service
```

## **Check service status**

```bash
sudo systemctl status zenrock-testnet.service
```

## **Check service logs**

```bash
sudo journalctl -u zenrock-testnet.service -f --no-hostname -o cat
```
