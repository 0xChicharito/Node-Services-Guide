# 💾 Command

### Service operations ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u prysmd -f
```

Start service

```bash
sudo systemctl start prysmd
```

Stop service

```bash
sudo systemctl stop prysmd
```

Restart service

```bash
sudo systemctl restart prysmd
```

Check service status

```bash
sudo systemctl status prysmd
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable prysmd
```

Disable Service

```bash
sudo systemctl disable prysmd
```

Node info

```bash
prysmd status 2>&1 | jq
```

Your node peer

```bash
echo $(prysmd tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.prysm/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Key management <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
prysmd keys add $WALLET
```

Restore executing wallet

```bash
prysmd keys add $WALLET --recover
```

List All Wallets

```bash
prysmd keys list
```

Delete wallet

```bash
prysmd keys delete $WALLET
```

Check Balance

```bash
prysmd q bank balances $WALLET_ADDRESS 
```

Export Key (save to wallet.backup)

```bash
prysmd keys export $WALLET
```

View EVM Prived Key

```bash
prysmd keys unsafe-export-eth-key $WALLET
```

Import Key (restore from wallet.backup)

```bash
prysmd keys import $WALLET wallet.backup
```

### Tokens <a href="#tokens" id="tokens"></a>

To valoper addressTo wallet addressAmount, uprysm

Withdraw all rewards

```bash
prysmd tx distribution withdraw-all-rewards --from $WALLET --chain-id prysm-devnet-1 --gas auto --gas-adjustment 1.5 
```

Withdraw rewards and commission from your validator

```bash
prysmd tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id prysm-devnet-1 --gas auto --gas-adjustment 1.5 -y 
```

Check your balance

```bash
prysmd query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
prysmd tx staking delegate $(prysmd keys show $WALLET --bech val -a) 1000000uprysm --from $WALLET --chain-id prysm-devnet-1 --gas auto --gas-adjustment 1.5 -y 
```

Delegate

```bash
prysmd tx staking delegate <TO_VALOPER_ADDRESS> 1000000uprysm --from $WALLET --chain-id prysm-devnet-1 --gas auto --gas-adjustment 1.5 -y 	
```

Redelegate Stake to Another Validator

```bash
prysmd tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000uprysm --from $WALLET --chain-id prysm-devnet-1 --gas auto --gas-adjustment 1.5 -y 
```

Unbond

```bash
prysmd tx staking unbond $(prysmd keys show $WALLET --bech val -a) 1000000uprysm --from $WALLET --chain-id prysm-devnet-1 --gas auto --gas-adjustment 1.5 -y 
```

Transfer Funds

```bash
prysmd tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000uprysm --gas auto --gas-adjustment 1.5 -y 
```

### Validator operations <a href="#validator-operations" id="validator-operations"></a>

MonikerIdentityDetailsAmount, uprysmCommission rateCommission max rateCommission max change rate

Create New Validator

```bash
prysmd tx staking create-validator \
--amount 1000000uprysm \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(prysmd tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--chain-id prysm-devnet-1 \
--gas auto --gas-adjustment 1.5 \
-y 
```

Edit Existing Validator

```bash
prysmd tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--from $WALLET \
--chain-id prysm-devnet-1 \
--gas auto --gas-adjustment 1.5 \
-y 
```

Validator info

```bash
prysmd status 2>&1 | jq
```

Validator Details

```bash
prysmd q staking validator $(prysmd keys show $WALLET --bech val -a) 
```

Jailing info

```bash
prysmd q slashing signing-info $(prysmd tendermint show-validator) 
```

Slashing parameters

```bash
prysmd q slashing params 
```

Unjail validator

```bash
prysmd tx slashing unjail --from $WALLET --chain-id prysm-devnet-1 --gas auto --gas-adjustment 1.5 -y 
```

Active Validators List

```bash
prysmd q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl 
```

Check Validator key

```bash
[[ $(prysmd q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(prysmd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
prysmd q slashing signing-info $(prysmd tendermint show-validator) 
```

### Governance <a href="#governance" id="governance"></a>

TitleDescriptionDeposit, uprysm

Create New Text Proposal

```bash
prysmd  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000uprysm \
--type Text \
--from $WALLET \
--gas auto --gas-adjustment 1.5 \
-y 
```

Proposals List

```bash
prysmd query gov proposals 
```

Proposal IDProposal optionYesNoNo with vetoAbstain

View proposal

```bash
prysmd query gov proposal 1 
```

Vote

```bash
prysmd tx gov vote 1 yes --from $WALLET --chain-id prysm-devnet-1  --gas auto --gas-adjustment 1.5 -y 
```

