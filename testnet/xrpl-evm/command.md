# 🕹️ Command

### Service operations ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u exrpd -fo cat
```

Start service

```bash
sudo systemctl start exrpd
```

Stop service

```bash
sudo systemctl stop exrpd
```

Restart service

```bash
sudo systemctl restart exrpd
```

Check service status

```bash
sudo systemctl status exrpd
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable exrpd
```

Disable Service

```bash
sudo systemctl disable exrpd
```

Node info

```bash
exrpd status 2>&1 | jq
```

Your node peer

```bash
echo $(exrpd tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.exrpd/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Key management <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
exrpd keys add $WALLET
```

Restore executing wallet

```bash
exrpd keys add $WALLET --recover
```

List All Wallets

```bash
exrpd keys list
```

Delete wallet

```bash
exrpd keys delete $WALLET
```

Check Balance

```bash
exrpd q bank balances $WALLET_ADDRESS 
```

Export Key (save to wallet.backup)

```bash
exrpd keys export $WALLET
```

View EVM Prived Key

```bash
exrpd keys unsafe-export-eth-key $WALLET
```

Import Key (restore from wallet.backup)

```bash
exrpd keys import $WALLET wallet.backup
```

### Tokens <a href="#tokens" id="tokens"></a>

To valoper addressTo wallet addressAmount, uxrp

Withdraw all rewards

```bash
exrpd tx distribution withdraw-all-rewards --from $WALLET --chain-id exrp_1440002-1 --gas auto --gas-adjustment 1.5 
```

Withdraw rewards and commission from your validator

```bash
exrpd tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id exrp_1440002-1 --gas auto --gas-adjustment 1.5 -y 
```

Check your balance

```bash
exrpd query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
exrpd tx staking delegate $(exrpd keys show $WALLET --bech val -a) 1000000uxrp --from $WALLET --chain-id exrp_1440002-1 --gas auto --gas-adjustment 1.5 -y 
```

Delegate

```bash
exrpd tx staking delegate <TO_VALOPER_ADDRESS> 1000000uxrp --from $WALLET --chain-id exrp_1440002-1 --gas auto --gas-adjustment 1.5 -y 	
```

Redelegate Stake to Another Validator

```bash
exrpd tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000uxrp --from $WALLET --chain-id exrp_1440002-1 --gas auto --gas-adjustment 1.5 -y 
```

Unbond

```bash
exrpd tx staking unbond $(exrpd keys show $WALLET --bech val -a) 1000000uxrp --from $WALLET --chain-id exrp_1440002-1 --gas auto --gas-adjustment 1.5 -y 
```

Transfer Funds

```bash
exrpd tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000uxrp --gas auto --gas-adjustment 1.5 -y 
```

### Validator operations <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
exrpd tx staking create-validator \
--amount 1000000uxrp \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(exrpd tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--chain-id exrp_1440002-1 \
--gas auto --gas-adjustment 1.5 \
-y 
```

Edit Existing Validator

```bash
exrpd tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--from $WALLET \
--chain-id exrp_1440002-1 \
--gas auto --gas-adjustment 1.5 \
-y 
```

Validator info

```bash
exrpd status 2>&1 | jq
```

Validator Details

```bash
exrpd q staking validator $(exrpd keys show $WALLET --bech val -a) 
```

Jailing info

```bash
exrpd q slashing signing-info $(exrpd tendermint show-validator) 
```

Slashing parameters

```bash
exrpd q slashing params 
```

Unjail validator

```bash
exrpd tx slashing unjail --from $WALLET --chain-id exrp_1440002-1 --gas auto --gas-adjustment 1.5 -y 
```

Active Validators List

```bash
exrpd q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl 
```

Check Validator key

```bash
[[ $(exrpd q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(exrpd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
exrpd q slashing signing-info $(exrpd tendermint show-validator) 
```

### Governance <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
exrpd  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000uxrp \
--type Text \
--from $WALLET \
--gas auto --gas-adjustment 1.5 \
-y 
```

Proposals List

```bash
exrpd query gov proposals 
```

View proposal

```bash
exrpd query gov proposal 1 
```

Vote

```bash
exrpd tx gov vote 1 yes --from $WALLET --chain-id exrp_1440002-1  --gas auto --gas-adjustment 1.5 -y
```
