# 🛰️ Usage Command

**Info**

```bash
fiammad status 2>&1 | jq .NodeInfo
fiammad status 2>&1 | jq .SyncInfo
fiammad status 2>&1 | jq .ValidatorInfo
```

**Check node logs**

```bash
sudo journalctl -fu fiammad -o cat
```

**Check service status**

```bash
sudo systemctl status fiammad 
```

**Restart service**

```bash
sudo systemctl restart fiammad 
```

**Stop service**

```bash
sudo systemctl stop fiammad 
```

**Start service**

```bash
sudo systemctl start fiammad 
```

**reload/disable/enable**

```bash
sudo systemctl daemon-reload
sudo systemctl disable fiammad 
sudo systemctl enable fiammad  
```

**Your Peer**

```bash
echo $(fiammad tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.fiamma/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### 🥅Working with keys <a href="#working-with-keys" id="working-with-keys"></a>

**New Key or Recover Key**

```bash
fiammad keys add Wallet_Name
      OR
fiammad  keys add Wallet_Name --recover
```

**Check all keys**

```bash
fiammad keys list
```

**Check Balance**

```bash
fiammad q bank balances $WALLET_ADDRESS 
```

**Delete Key**

```bash
fiammad keys delete Wallet_Name
```

**Export Key**

```bash
fiammad keys export wallet
```

**Import Key**

```bash
fiammad keys import wallet wallet.backup
```

### 🚀Validator Management <a href="#validator-management" id="validator-management"></a>

**Edit Validator**

```bash
fiammad tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--from $WALLET \
--chain-id fiamma-testnet-1 \
--gas auto --gas-adjustment 1.5 \
-y 
```

**Your Valoper-Address**

```bash
fiammad keys show Wallet_Name --bech val
```

**Your Valcons-Address**

```bash
fiammad tendermint show-address
```

**Your Validator-Info**

```bash
fiammad query staking validator fiammavaloperaddress......
```

**Jail Info**

```bash
fiammad query slashing signing-info $(fiammad tendermint show-validator)
```

**Unjail**

```bash
fiammad tx slashing unjail --from Wallet_name --fees=500ufia --chain-id=fiamma-testnet-1 -y
```

**Active Validators List**

```bash
fiammad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**Inactive Validators List**

```bash
fiammad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**Check that your key matches the validator (Win - Good. Lose - Bad)**

```bash
VALOPER=Enter_Your_valoper_Here
[[ $(fiammad q staking validator $VALOPER -oj | jq -r .consensus_pubkey.key) = $(fiammad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\nYou win\n" || echo -e "\nYou lose\n"
```

**Withdraw all rewards from all validators**

```bash
fiammad tx distribution withdraw-all-rewards --from Wallet_Name --chain-id fiamma-testnet-1 --fees=500ufia -y
```

**Withdraw and commission from your Validator**

```bash
fiammad  tx distribution withdraw-rewards valoper1a........ --from Wallet_Name --fees=500ufia --chain-id=fiamma-testnet-1--commission -y
```

**Delegate tokens to your validator**

```bash
fiammad tx staking delegate $(fiammad keys show $WALLET --bech val -a) 1000000ufia --from $WALLET --chain-id fiamma-testnet-1 --gas auto --gas-adjustment 1.5 -y 
```

**Delegate tokens to different validator**

```bash
fiammad tx staking delegate <TO_VALOPER_ADDRESS> 1000000ufia --from $WALLET --chain-id fiamma-testnet-1 --gas auto --gas-adjustment 1.5 -y 	
```

**Redelegate tokens to another validator**

```bash
fiammad tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000ufia --from $WALLET --chain-id fiamma-testnet-1 --gas auto --gas-adjustment 1.5 -y 
```

**Unbond tokens from your validator or different validator**

```bash
fiammad tx staking unbond $(fiammad keys show $WALLET --bech val -a) 1000000ufia --from $WALLET --chain-id fiamma-testnet-1 --gas auto --gas-adjustment 1.5 -y 
```

**Transfer tokens from wallet to wallet**

```bash
fiammad tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000ufia --gas auto --gas-adjustment 1.5 -y 
```

### 📝Governance <a href="#governance" id="governance"></a>

**View all proposals**

```bash
fiammad query gov proposals
```

**View specific proposal**

```bash
fiammad query gov proposal 1
```

**Vote yes**

```bash
fiammad tx gov vote 1 yes --from Wallet_Name --gas 350000  --chain-id=fiamma-testnet-1 -y
```

**Vote no**

```bash
fiammad tx gov vote 1 no --from Wallet_Name --gas 350000  --chain-id=fiamma-testnet-1 -y
```

**Vote abstain**

```bash
fiammad tx gov vote 1 abstain --from Wallet_Name --gas 350000  --chain-id=fiamma-testnet-1 -y
```

**Vote no\_with\_veto**

```bash
fiammad tx gov vote 1 no_with_veto --from Wallet_Name --gas 350000  --chain-id=fiamma-testnet-1 -y
```
