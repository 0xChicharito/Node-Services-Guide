# 🛰️ Command

#### Check logs

```bash
sudo journalctl -u kopid -f
```

#### API

```bash
sudo systemctl status kopid -f
```

#### Node info

```bash
kopid status 2>&1 | jq -f
```

#### Start service

```bash
sudo systemctl start kopid
```

#### Reload services

```bash
sudo systemctl daemon-reload
```

#### Stop service

```bash
sudo systemctl stop kopid
```

#### Enable Service

```bash
sudo systemctl enable kopid
```

#### Restart service

```bash
sudo systemctl restart  kopid
```

#### Disable Service

```bash
sudo systemctl disable kopid
```

#### Your node peer

```bash
echo $(kopid tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.kopid/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

## Key Management

#### Add New Wallet

```bash
kopid keys add $WALLET
```

#### Restore wallet

```bash
kopid keys add $WALLET --recover
```

#### List All Wallets

```bash
kopid keys list
```

#### Delete wallet

```bash
kopid keys delete $WALLET
```

#### Check Balance

```bash
kopid q bank balances $WALLET_ADDRESS
```

#### Export Key (save to wallet.backup)

```bash
kopid keys export $WALLET
```

#### View EVM Prived Key

```bash
kopid keys unsafe-export-eth-key $WALLET
```

#### Import Key (restore from wallet.backup)

```bash
kopid keys import $WALLET wallet.backup
```

## Token Management

#### Withdraw rewards and commission from your validator

```bash
kopid tx distribution withdraw-rewards  --from  --commission --chain-id kopi-test-5 --gas auto --gas-adjustment 1.5 --fees 600ukopi -y
```

#### Check your balance

```bash
kopid query bank balances 
```

#### Delegate to Yourself

```bash
kopid tx staking delegate $(kopid keys show  --bech val -a) ukopi --from  --chain-id kopi-test-5 --gas auto --gas-adjustment 1.5 --fees 600ukopi -y
```

#### Delegate

```bash
kopid tx staking delegate ukopi --from  --chain-id kopi-test-5 --gas auto --gas-adjustment 1.5 --fees 600ukopi -y
```

#### Edit Validator

```bash
kopid tx staking redelegate  ukopi --from  --chain-id kopi-test-5 --gas auto --gas-adjustment 1.5 --fees 600ukopi -y
```

#### Unbond Your Stake

```bash
kopid tx staking unbond $(kopid keys show  --bech val -a) ukopi --from  --chain-id kopi-test-5 --gas auto --gas-adjustment 1.5 --fees 600ukopi -y
```

#### Transfer Funds to Another Wallet

```bash
kopid tx bank send  <TO_WALLET_ADDRESS> ukopi --gas auto --gas-adjustment 1.5 --fees 600ukopi-y
```

## Validator Management

#### Create New Validator

```bash
kopid tx staking create-validator \
--amount 1000000kopid \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $($kopid tendermint show-validator) \
--moniker "$MONIKER" \
--identity "$MyIdentity" \
--details " Let's aim for a bright future 🔮" \
--chain-id kopi-test-5 \
--gas auto --gas-adjustment 1.5 --fees 600kopid \
-y
```

#### Edit Validator

```bash
kopid tx staking create-validator \
--kopid tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details " Let's aim for a bright future 🔮" \
--from $WALLET \
--chain-id kopi-test-5 \
--gas auto --gas-adjustment 1.5 --fees 600kopid \
-y 
```

#### Validator info

```bash
kopid status 2>&1 | jq
```

#### Validator Details

```bash
kopid q staking validator $(kopid keys show $WALLET --bech val -a)
```

#### Jailing info

```bash
kopid q slashing signing-info $(kopid tendermint show-validator)
```

#### Slashing parameters

```bash
kopid q slashing params
```

#### Unjail validator

```bash
kopid tx slashing unjail --from $WALLET --chain-id kopi-test-5 --gas auto --gas-adjustment 1.5 --fees 600kopid -y
```

#### Active Validators List

```bash
kopid q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

#### Check Validator key

```bash
[[ $(kopid q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(kopid status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

#### Signing info

```bash
kopid q slashing signing-info $(kopid tendermint show-validator)
```
