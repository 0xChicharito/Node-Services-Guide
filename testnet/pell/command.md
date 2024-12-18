# 🕹️ Command

Withdraw all rewards

```bash
pellcored tx distribution withdraw-all-rewards --from $WALLET --chain-id ignite_186-1 --gas auto --gas-adjustment 1.5 
```

Withdraw rewards and commission from your validator

```bash
pellcored tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id ignite_186-1 --gas auto --gas-adjustment 1.5 -y 
```

Check your balance

```bash
pellcored query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
pellcored tx staking delegate $(pellcored keys show $WALLET --bech val -a) 1000000pell --from $WALLET --chain-id ignite_186-1 --gas auto --gas-adjustment 1.5 -y 
```

Delegate

```bash
pellcored tx staking delegate $(pellcored keys show "Chicharito" --bech val -a) 25000000000000000000apell --from "Chicharito" --chain-id ignite_186-1 --gas-adjustment 1.5 --gas auto --gas-prices 33apell -y
```

Redelegate Stake to Another Validator

```bash
pellcored tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000pell --from $WALLET --chain-id ignite_186-1 --gas auto --gas-adjustment 1.5 -y 
```

Unbond

```bash
pellcored tx staking unbond $(pellcored keys show $WALLET --bech val -a) 1000000pell --from $WALLET --chain-id ignite_186-1 --gas auto --gas-adjustment 1.5 -y 
```

Transfer Funds

```bash
pellcored tx bank send "Chicharito" pell1x4u6fmv8a3ezrnxu8xxxxxxxx 1pell --from Chicharito --chain-id ignite_186-1 --fees=0.000001pell --gas=1000000 -y
```

Vote

```bash
pellcored tx gov vote 7 yes --from Chicharito --chain-id ignite_186-1 --gas auto --gas-adjustment 1.5 --gas-prices 33apell -y 
```
