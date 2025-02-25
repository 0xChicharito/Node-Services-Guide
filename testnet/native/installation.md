# ⚙️ Installation

<pre class="language-bash"><code class="lang-bash"><strong># install dependencies, if needed
</strong>sudo apt update &#x26;&#x26; sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
</code></pre>

{% hint style="info" %}
Port = 20
{% endhint %}

<pre class="language-bash"><code class="lang-bash"># install go, if needed
cd $HOME
VER="1.22.6"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] &#x26;&#x26; touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] &#x26;&#x26; mkdir -p ~/go/bin

# set vars
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="Chicharito"" >> $HOME/.bash_profile
echo "export NATIVE_CHAIN_ID="native-t1"" >> $HOME/.bash_profile
echo "export NATIVE_PORT="20"" >> $HOME/.bash_profile
source $HOME/.bash_profile

# download binary
cd $HOME
git clone https://github.com/gonative-cc/gonative &#x26;&#x26; cd gonative
git checkout v0.1.1
make build
mv $HOME/gonative/out/gonative $HOME/go/bin/gonatived

gonatived version --long | grep -e version -e commit
# version: 0.1.1
# commit: 5bcbb57e4e4689cf515e1c9e6326ae9078f3bdfa
# comet_server_version: v1.0.0-beta.1
# cosmos_sdk_version: v0.52.0-rc.1.0.20250106202622-c6327ea6e403
# runtime_version: v2.0.0-20241219154748-69025c556666
# stf_version: v1.0.0-beta.1

# config and init app
gonatived config node tcp://localhost:${NATIVE_PORT}657
gonatived config set client chain-id native-t1
gonatived config set client keyring-backend os
gonatived init "Chicharito" --chain-id native-t1

# download genesis and addrbook
wget https://github.com/gonative-cc/network-docs/raw/refs/heads/master/genesis/genesis-testnet-1.json.gz
gzip -d genesis-testnet-1.json.gz
mv genesis-testnet-1.json  $HOME/.gonative/config/genesis.json

sha256sum ~/.gonative/config/genesis.json
# 6c7c7ee70b89850dfe9b9c64b24debe67e36ddf5457c8555d4b979146b99e1b0
wget -O $HOME/.gonative/config/addrbook.json "https://share102.utsa.tech/native/addrbook.json"

# set seeds and peers
peers="2e2f0def6453e67a5d5872da7f73002caf55a010@195.3.221.110:52656,612e6279e528c3fadfe0bb9916fd5532bc9be2cd@164.132.247.253:56406,f2e45e48f55f649dee0e8dc4adfa445308ae8018@167.235.247.51:26656,a7577f50cdefd9a7a5e4a673278d9004df9b4bb4@103.219.169.97:56406,236946946eacbf6ab8a6f15c99dac1c80db6f8a5@65.108.203.61:52656,13b49060d7ae1375a04a8eaec8602920f47b5a55@37.142.120.99:27656,48bd9b42755de1a3039089c4cb66b9dd01561001@57.129.53.32:40656,0c2926cdac1e31e39ea137ec3438be6485fd58d7@116.202.85.179:10007,b80d0042f7096759ae6aada870b52edf0dcd74af@65.109.58.158:26056,f0e48f295e0d7c7d03943c82ac01bfc54969320b@78.46.185.199:26656,8eb8024d28b070d21844a90c7c2d34ef4b731365@193.34.213.77:15007,b5f52d67223c875947161ea9b3a95dbec30041cb@116.202.42.156:32107,5be5b41a6aef28a7779002f2af0989c7a7da5cfe@165.154.245.110:26656,b07e0482f43a2add3531e9aa7946609386b2e7e7@65.109.113.219:30656,6edabc54e76245af9f8f739303c788bfb23bd09a@95.216.12.106:24096,ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@176.9.82.221:30656,d0e0d80be68cec942ad46b36419f0ba76d35d134@94.130.138.41:26444,be5b6092815df2e0b2c190b3deef8831159bb9a2@64.225.109.119:26656,49784fe6a1b812fd45f4ac7e5cf953c2a3630cef@136.243.17.170:38656,40cbf43553e7ee3507814e3110a749a7e638aa83@194.163.169.182:24656,1e9a3f3615ec98ea66c41b7e37a724889067bc05@193.24.209.155:12056,7567880ef17ce8488c55c3256c76809b37659cce@161.35.157.54:26656,d856c6c6f195b791c54c18407a8ad4391bd30b99@142.132.156.99:24096,2dacf537748388df80a927f6af6c4b976b7274cb@148.251.44.42:26656,2c1e6b6b54daa7646339fa9abede159519ca7cae@37.252.186.248:26656,fbc51b668eb84ae14d430a3db11a5d90fc30f318@65.108.13.154:52656,48d0fdcc642690ede0ad774f3ba4dce6e549b4db@142.132.215.124:26656,24d51644ea1a6b91bf745f6767a9079d4b35e3d2@37.27.202.188:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.gonative/config/config.toml
seeds="ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@testnet-seeds.polkachu.com:30656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.gonative/config/config.toml


# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${NATIVE_PORT}317%g;
s%:8080%:${NATIVE_PORT}080%g;
s%:9090%:${NATIVE_PORT}090%g;
s%:9091%:${NATIVE_PORT}091%g;
s%:8545%:${NATIVE_PORT}545%g;
s%:8546%:${NATIVE_PORT}546%g;
s%:6065%:${NATIVE_PORT}065%g" $HOME/.gonative/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${NATIVE_PORT}658%g;
s%:26657%:${NATIVE_PORT}657%g;
s%:6060%:${NATIVE_PORT}060%g;
s%:26656%:${NATIVE_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${NATIVE_PORT}656\"%;
s%:26660%:${NATIVE_PORT}660%g" $HOME/.gonative/config/config.toml

# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.gonative/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.gonative/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.gonative/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.1untiv"|g' $HOME/.gonative/config/app.toml
<strong>sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.gonative/config/config.toml
</strong>sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.gonative/config/config.toml

# create service file
sudo tee /etc/systemd/system/gonatived.service > /dev/null &#x3C;&#x3C;EOF
[Unit]
Description=gonatived
After=network-online.target

[Service]
User=$USER
ExecStart=$(which gonatived) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

# reset and download snapshot
if curl -s --head curl https://share102.utsa.tech/native/native_testnet.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://share102.utsa.tech/native/native_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.gonatived
    else
  echo "no snapshot founded"
fi

# enable and start service
systemctl daemon-reload
systemctl enable gonatived
systemctl restart gonatived &#x26;&#x26; journalctl -u gonatived -f -o cat
</code></pre>

### Create wallet <a href="#create-wallet" id="create-wallet"></a>

```bash
# to create a new wallet, use the following command. don’t forget to save the mnemonic
gonatived keys add $WALLET

# to restore exexuting wallet, use the following command
gonatived keys add $WALLET --recover

# save wallet and validator address
WALLET_ADDRESS=$(gonatived keys show $WALLET -a)
VALOPER_ADDRESS=$(gonatived keys show $WALLET --bech val -a)
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile

# check sync status, once your node is fully synced, the output from above will print "false"
gonatived status 2>&1 | jq 

# before creating a validator, you need to fund your wallet and check balance
gonatived query bank balances $WALLET_ADDRESS 
```

### Node Sync Status Checker <a href="#node-sync-status" id="node-sync-status"></a>

```bash
#!/bin/bash
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.prysm/config/config.toml" | cut -d ':' -f 3)
while true; do
  local_height=$(curl -s localhost:$rpc_port/status | jq -r '.result.sync_info.latest_block_height')
  network_height=$(curl -s https://prysm-testnet-rpc.itrocket.net/status | jq -r '.result.sync_info.latest_block_height')

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

### Create validator <a href="#create-validator" id="create-validator"></a>

```bash
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(prysmd comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000uprysm\",
    \"moniker\": \"\",
    \"identity\": \"\",
    \"website\": \"\",
    \"security\": \"\",
    \"details\": \"I love Prysm ❤️\",
    \"commission-rate\": \"0.1\",
    \"commission-max-rate\": \"0.2\",
    \"commission-max-change-rate\": \"0.01\",
    \"min-self-delegation\": \"1\"
}" > validator.json
# Create a validator using the JSON configuration
prysmd tx staking create-validator validator.json \
    --from $WALLET \
    --chain-id prysm-devnet-1 \
    --gas auto --gas-adjustment 1.5 \
    -y
	
```

### Delete node <a href="#delete" id="delete"></a>

```bash
sudo systemctl stop gonatived
sudo systemctl disable gonatived
sudo rm -rf /etc/systemd/system/gonatived.service
sudo rm $(which gonatived)
sudo rm -rf $HOME/.gonative
sed -i "/NATIVE_/d" $HOME/.bash_profile
```
