# ⚙️ Installation

### Update & upgrade tools:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install make net-tools curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

### Install GO:

```bash
cd $HOME
VER="1.23.2"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

or just this to use go on other user:

```bash
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

### Cloning Kopi repository and installing it locally:

```bash
rm -rf ${HOME}/kopi
git clone --quiet --depth 1 --branch v0.6.5 https://github.com/kopi-money/kopi.git ${HOME}/kopi
cd ${HOME}/kopi
make install
```

Check Kopi version

```bash
$HOME/go/bin/kopid version --long | tail
```

### INITIA NODE:

#### Set var:

> Port=25

```bash
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="your-moniker"" >> $HOME/.bash_profile
echo "export KOPI_CHAIN_ID="luwak-1"" >> $HOME/.bash_profile
echo "export KOPI_PORT="25"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
DAEMON_HOME="$HOME/.kopid/" DAEMON_NAME="kopid" cosmovisor init $HOME/go/bin/kopid
```

#### config and init app

```bash
kopid init $MONIKER && \
kopid config set client chain-id $KOPI_CHAIN_ID && \
kopid config set client keyring-backend test && \
sed -i -e "s|^node *=.*|node = \"tcp://localhost:${KOPI_PORT}657\"|" $HOME/.kopid/config/client.toml
```

### Custom Port:

#### set custom ports in `app.toml`

```bash
sed -i.bak -e "s%:1317%:${KOPI_PORT}317%g;
s%:8080%:${KOPI_PORT}080%g;
s%:9090%:${KOPI_PORT}090%g;
s%:9091%:${KOPI_PORT}091%g;
s%:8545%:${KOPI_PORT}545%g;
s%:8546%:${KOPI_PORT}546%g;
s%:6065%:${KOPI_PORT}065%g" $HOME/.kopid/config/app.toml
```

#### set custom ports in `config.toml` file

```bash
sed -i.bak -e "s%:26658%:${KOPI_PORT}658%g;
s%:26657%:${KOPI_PORT}657%g;
s%:6060%:${KOPI_PORT}060%g;
s%:26656%:${KOPI_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${KOPI_PORT}656\"%;
s%:26660%:${KOPI_PORT}660%g" $HOME/.kopid/config/config.toml
```

### Downloading genesis and adjust config files...:

```bash
IP=$(curl -s -4 icanhazip.com)
sed -i -e "s/external_address = \"\"/external_address = \"${IP}:26656\"/g" ~/.kopid/config/config.toml
sed -i '/^seeds/ c\seeds = "85919e3dcc7eec3b64bfdd87657c4fac307c9d23@65.109.34.145:26656"' ~/.kopid/config/config.toml
sed -i -e 's/timeout_propose = "3s"/timeout_propose = "1s"/g' ~/.kopid/config/config.toml
sed -i -e 's/timeout_commit = "5s"/timeout_commit = "1s"/g' ~/.kopid/config/config.toml
sed -i -e 's/minimum-gas-prices = ""/minimum-gas-prices = "0ukopi"/g' ~/.kopid/config/app.toml
```

#### Setting pruning

```bash
sed -i -e 's|^pruning *=.*|pruning = "custom"|' $HOME/.kopid/config/app.toml
sed -i -e 's|^pruning-keep-recent  *=.*|pruning-keep-recent = "100"|' $HOME/.kopid/config/app.toml
sed -i -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' $HOME/.kopid/config/app.toml
sed -i -e 's|^pruning-interval *=.*|pruning-interval = "10"|' $HOME/.kopid/config/app.toml
```

#### Disable indexer

```bash
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.kopid/config/config.toml
```

#### Enable Prometheus

```bash
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.kopid/config/config.toml
```

#### Downloading chain data:

```bash
wget -q https://raw.githubusercontent.com/kopi-money/mainnet-config/refs/heads/master/genesis.json > $HOME/.kopid/config/genesis.json
```

#### Addrbook:

```bash
curl -Ls https://snapshot.sychonix.com/mainnet/kopi/addrbook.json > $HOME/.kopid/config/addrbook.json
```

### Create service:

```bash
sudo tee /etc/systemd/system/kopid.service > /dev/null <<EOF

[Unit]
Description=Kopi node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.kopid
ExecStart=$(which kopid) start --home $HOME/.kopid
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

<pre class="language-bash"><code class="lang-bash"><strong>#reset and download snapshot
</strong>kopid tendermint unsafe-reset-all --home $HOME/.kopid
if curl -s --head curl https://snapshot.node9x.com/kopi-snapshot.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://snapshot.node9x.com/kopi_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.kopid
    else
  echo "no snapshot found"
fi
</code></pre>

#### Service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable kopid.service
sudo systemctl start kopid
```

Stop service:

```bash
sudo systemctl stop kopid
sudo systemctl disable kopid
```

#### CHECK:

**Check status:**

```bash
sudo systemctl status kopid
```

**Check log:**

```bash
sudo journalctl -u kopid -f --no-hostname -o cat
```

**Check Sync:**

```bash
kopid status 2>&1 | jq .sync_info
```

**Check validator info:**

```bash
kopid status 2>&1 | jq .validator_info
```

### Delete node:

_**Make sure you have backup your validator before do this action:**_

```bash
sudo systemctl stop kopid
sudo systemctl disable kopid
sudo rm -rf /etc/systemd/system/kopid.service
sudo systemctl daemon-reload
sudo rm -f $(which kopid)
sudo rm -rf $HOME/.kopid
sudo rm -rf $HOME/kopi
```

#### Node Sync Status Checker <a href="#node-sync-status" id="node-sync-status"></a>

```bash
#!/bin/bash
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.kopid/config/config.toml" | cut -d ':' -f 3)
while true; do
  local_height=$(curl -s localhost:$rpc_port/status | jq -r '.result.sync_info.latest_block_height')
  network_height=$(curl -s https://kopi-rpc.node9x.com/status | jq -r '.result.sync_info.latest_block_height')

  if ! [[ "$local_height" =~ ^[0-9]+$ ]] || ! [[ "$network_height" =~ ^[0-9]+$ ]]; then
    echo -e "\033[1;31mError: Invalid block height data. Retrying...\033[0m"
    sleep 5
    continue
  fi

  blocks_left=$((network_height - local_height))
  if [ "$blocks_left" -lt 0 ]; then
    blocks_left=0
  fi

  echo -e "\033[1;33mNode Height:\033[1;34m $local_height\033[0m \033[1;33m| Network Height:\033[1;36m $network_height\033[0m \033[1;33m| Blocks Left:\033[1;31m $blocks_left\033[0m"

  sleep 5
done


```

### Create Validator

```bash
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(kopid comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000ukopi\",
    \"moniker\": \"\",
    \"identity\": \"\",
    \"website\": \"\",
    \"security\": \"\",
    \"details\": \"I love Kopi ❤️\",
    \"commission-rate\": \"0.1\",
    \"commission-max-rate\": \"0.2\",
    \"commission-max-change-rate\": \"0.01\",
    \"min-self-delegation\": \"1\"
}" > validator.json
# Create a validator using the JSON configuration
kopid tx staking create-validator $HOME/validator.json \
  --from $WALLET \
  --chain-id luwak-1 \
  -y
```

