# 🕹️ Command

## Validator Setup

### Create/restore a key pair[​](https://docs.wardenprotocol.org/operate-a-node/create-a-validator#1-createrestore-a-key-pair) <a href="#id-1-createrestore-a-key-pair" id="id-1-createrestore-a-key-pair"></a>

* Create Wallet

```bash
bitbadgeschaind keys add <your-wallet-name>
```

* Restore Wallet

```bash
bitbadgeschaind keys add <your-wallet-name> --recover
```

### Create Your Validator <a href="#create-your-validator" id="create-your-validator"></a>

```bash
PUBKEY=$(bitbadgeschaind tendermint show-validator)

cat > $HOME/.bitbadgeschain/validatortx.txt << EOM
{
  "pubkey": $PUBKEY,
  "amount": "1000000000ubadge",
  "moniker": "",
  "identity": "", 
  "website": "",
  "security": "",
  "details": "",
  "commission-rate": "0.1",
  "commission-max-rate": "0.2",
  "commission-max-change-rate": "0.01",
  "min-self-delegation": "1"
}
EOM
```

To create your validator use the following command:

```bash
bitbadgeschaind tx staking create-validator $HOME/.bitbadgeschain/validatortx.txt \
  --from=<your-wallet-name> \
  --chain-id="bitbadges-1" \
  --gas="auto" \
  --gas-prices="0.025ubadge" \
  --gas-adjustment=1.5 \
  --yes

```

Withdraw rewards and commission

```bash
bitbadgeschaind tx distribution withdraw-rewards bbvaloper1umps8fsm0s5g2s87y79m74szr8ayf8kcv53qmc --from Chicharito --commission --chain-id bitbadges-1 --gas auto --gas-adjustment 1.5 --gas-prices 21ubadge -y
```

Delegate to Yourself

```bash
bitbadgeschaind tx staking delegate $(bitbadgeschaind keys show Chicharito --bech val -a) 65000000000ubadge --from Chicharito --chain-id bitbadges-1 --gas auto --gas-adjustment 1.5 --gas-prices 21ubadge -y
```

#### Governance <a href="#governance" id="governance"></a>

Vote

```bash
bitbadgeschaind tx gov vote 19 yes \
  --from Chicharito \
  --chain-id bitbadges-1 \
  --gas auto \
  --gas-adjustment 2.0 \
  --gas-prices 21ubadge \
  -y
```

[\
](https://docs.provewithryd.xyz/mainnet/bitbadges/snapshot-and-state-sync)
