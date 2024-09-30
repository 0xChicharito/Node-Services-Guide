# Commands

### Service operations ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u 0gchaind -f
```

Start service

```bash
sudo systemctl start 0gchaind
```

Stop service

```bash
sudo systemctl stop 0gchaind
```

Restart service

```bash
sudo systemctl restart 0gchaind
```

Check service status

```bash
sudo systemctl status 0gchaind
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable 0gchaind
```

Disable Service

```bash
sudo systemctl disable 0gchaind
```

Node info

```bash
0gchaind status 2>&1 | jq
```

Your node peer

```bash
echo $(0gchaind tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.0gchain/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Key management <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
0gchaind keys add $WALLET
```

Restore executing wallet

```bash
0gchaind keys add $WALLET --recover
```

List All Wallets

```bash
0gchaind keys list
```

Delete wallet

```bash
0gchaind keys delete $WALLET
```

Check Balance

```bash
0gchaind q bank balances $WALLET_ADDRESS 
```

Export Key (save to wallet.backup)

```bash
0gchaind keys export $WALLET
```

View EVM Prived Key

```bash
0gchaind keys unsafe-export-eth-key $WALLET
```

Import Key (restore from wallet.backup)

```bash
0gchaind keys import $WALLET wallet.backup
```

### Tokens <a href="#tokens" id="tokens"></a>

To valoper addressTo wallet addressAmount, ua0gi

Withdraw all rewards

```bash
0gchaind tx distribution withdraw-all-rewards --from $WALLET --chain-id zgtendermint_16600-2 --gas=auto --gas-adjustment=1.6 
```

Withdraw rewards and commission from your validator

```bash
0gchaind tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id zgtendermint_16600-2 --gas=auto --gas-adjustment=1.6 -y 
```

Check your balance

```bash
0gchaind query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
0gchaind tx staking delegate $(0gchaind keys show $WALLET --bech val -a) 1000000ua0gi --from $WALLET --chain-id zgtendermint_16600-2 --gas=auto --gas-adjustment=1.6 -y 
```

Delegate

```bash
0gchaind tx staking delegate <TO_VALOPER_ADDRESS> 1000000ua0gi --from $WALLET --chain-id zgtendermint_16600-2 --gas=auto --gas-adjustment=1.6 -y 	
```

Redelegate Stake to Another Validator

```bash
0gchaind tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000ua0gi --from $WALLET --chain-id zgtendermint_16600-2 --gas=auto --gas-adjustment=1.6 -y 
```

Unbond

```bash
0gchaind tx staking unbond $(0gchaind keys show $WALLET --bech val -a) 1000000ua0gi --from $WALLET --chain-id zgtendermint_16600-2 --gas=auto --gas-adjustment=1.6 -y 
```

Transfer Funds

```bash
0gchaind tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000ua0gi --gas=auto --gas-adjustment=1.6 -y 
```

### Validator operations <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
0gchaind tx staking create-validator \
--amount 1000000ua0gi \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(0gchaind tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--chain-id zgtendermint_16600-2 \
--gas=auto --gas-adjustment=1.6 \
-y 
```

Edit Existing Validator

```bash
0gchaind tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--from $WALLET \
--chain-id zgtendermint_16600-2 \
--gas=auto --gas-adjustment=1.6 \
-y 
```

Validator info

```bash
0gchaind status 2>&1 | jq
```

Validator Details

```bash
0gchaind q staking validator $(0gchaind keys show $WALLET --bech val -a) 
```

Jailing info

```bash
0gchaind q slashing signing-info $(0gchaind tendermint show-validator) 
```

Slashing parameters

```bash
0gchaind q slashing params 
```

Unjail validator

```bash
0gchaind tx slashing unjail --from $WALLET --chain-id zgtendermint_16600-2 --gas=auto --gas-adjustment=1.6 -y 
```

Active Validators List

```bash
0gchaind q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl 
```

Check Validator key

```bash
[[ $(0gchaind q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(0gchaind status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
0gchaind q slashing signing-info $(0gchaind tendermint show-validator) 
```

### Governance <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
0gchaind  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000ua0gi \
--type Text \
--from $WALLET \
--gas=auto --gas-adjustment=1.6 \
-y 
```

Proposals List

```bash
0gchaind query gov proposals 
```

View proposal

```bash
0gchaind query gov proposal 1 
```

Vote

```bash
0gchaind tx gov vote 5 yes --from Chicharito --chain-id zgtendermint_16600-2 --gas-adjustment 1.5 --gas auto --gas-prices 0.00252ua0gi -y
```

#### Block sync left: <a href="#block-sync-left" id="block-sync-left"></a>

```
local_height=$(0gchaind status | jq -r .sync_info.latest_block_height); 
network_height=$(curl -s https://rpc.0gchain.josephtran.xyz/status | jq -r .result.sync_info.latest_block_height); 
blocks_left=$((network_height - local_height)); 

echo -e "\033[1;32mYour node height:\033[0m \033[1;34m$local_height\033[0m | \033[1;35mNetwork height:\033[0m \033[1;36m$network_height\033[0m | \033[1;29mBlocks left:\033[0m \033[1;31m$blocks_left\033[0m"
```

#### Get node peers: <a href="#get-node-peers" id="get-node-peers"></a>

```
echo $(0gchaind tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.0gchain/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

#### Fetch RPC port: <a href="#fetch-rpc-port" id="fetch-rpc-port"></a>

```
RPC="http://$(wget -qO- eth0.me)$(grep -A 3 "\[rpc\]" $HOME/.0gchain/config/config.toml | egrep -o ":[0-9]+")" && echo $RPC
```

```
curl $RPC/status | jq
```

#### Retrieving Node ID and Server IP Address Configuration: <a href="#retrieving-node-id-and-server-ip-address-configuration" id="retrieving-node-id-and-server-ip-address-configuration"></a>

**Your node:**

```
echo $(0gchaind tendermint show-node-id)'@'$(curl -s ipv4.icanhazip.com)':'$(cat $HOME/.0gchain/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**From RPC other node:**

```
curl -s https://rpc.0gchain.josephtran.xyz/net_info | jq -r '.result.peers[] | select(.node_info.listen_addr | test("^tcp://0.0.0.0") | not) | "\(.node_info.id)@\(.node_info.listen_addr)"'
```

#### Change RPC port: <a href="#change-rpc-port" id="change-rpc-port"></a>

Open for Client:

```
NEW_RPC_LADDR="tcp://0.0.0.0:26657"
NEW_EVM_ADDRESS="0.0.0.0:8545"
NEW_WS_ADDRESS="0.0.0.0:8546"
NEW_API="eth,txpool,personal,net,debug,web3"
```

```
sed -i \
    -e "s#^\(laddr = \)\"[^\"]*\"#\1\"${NEW_RPC_LADDR}\"#" \
    $HOME/.0gchain/config/config.toml
```

```
sed -i \
    -e "s#^\(address = \)\"[^\"]*\"#\1\"${NEW_EVM_ADDRESS}\"#" \
    -e "s#^\(ws-address = \)\"[^\"]*\"#\1\"${NEW_WS_ADDRESS}\"#" \
    -e "s#^\(api = \)\"[^\"]*\"#\1\"${NEW_API}\"#" \
    $HOME/.0gchain/config/app.toml
```
