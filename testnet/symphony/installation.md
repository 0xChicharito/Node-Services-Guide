# 🔌 Installation

### **1. Minimum hardware requirement** <a href="#id-1.-minimum-hardware-requirement" id="id-1.-minimum-hardware-requirement"></a>

4 Cores, 8G Ram, 160G SSD, Ubuntu 22.04

### 2. Server preparation <a href="#id-2.-server-preparation" id="id-2.-server-preparation"></a>

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install make curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

### 3. Install GO <a href="#id-3.-install-go" id="id-3.-install-go"></a>

```bash
#Install GO
ver="1.22.2"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```

### 4. Download and build binaries <a href="#id-4.-download-and-build-binaries" id="id-4.-download-and-build-binaries"></a>

```bash
cd $HOME
rm -rf symphony
git clone https://github.com/Orchestra-Labs/symphony
cd symphony
git checkout v0.2.1
make install
```

### 5. Initialize the node <a href="#id-5.-initialize-the-node" id="id-5.-initialize-the-node"></a>

```bash
cd $HOME
MONIKER=NodeName
echo "export MONIKER=$MONIKER" >> $HOME/.bash_profile
echo "export CHAIN_ID="symphony-testnet-2"" >> $HOME/.bash_profile
echo "export SYMPHONY_PORT="15"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```
symphonyd init $MONIKER --chain-id $CHAIN_ID
```

```bash
wget -O $HOME/.symphonyd/config/genesis.json https://files.nodesync.top/Symphony/symphony-genesis.json
wget -O $HOME/.symphonyd/config/addrbook.json https://files.nodesync.top/Symphony/symphony-addrbook.json
```

```bash
#Configure Seeds and Peers
SEEDS=""
PEERS="688b148e0a99b45c6b6ca6fbeae42f7a86c8ad4b@65.21.202.124:24856,adc09b9238bc582916abda954b081220d6f9cbc2@34.172.132.224:26656,eea2dc7e9abfd18787d4cc2c728689ad658cd3a2@35.184.9.159:26656,785f5e73e26623214269909c0be2df3f767fbe50@35.225.73.240:26656,8df964c61393d33d11f7c821aba1a72f428c0d24@34.41.129.120:26656,0d5510ec7d6aefc11b562d59f503e4f720fdbc6e@79.55.138.82:26656,67d3164069c7a5d9b1a3f02783f355a47c8f4699@135.125.67.229:26656,22e9b542b7f690922e846f479878ab391e69c4c3@57.129.35.242:26656,462ba3f9e328f677e984d2cc21b687d42df45370@195.201.194.231:26656,198c48664a3539072d7ebdfd6cc114bddcfdd02f@103.79.130.134:11347,27c6b80a1235d41196aa56459689c28f285efd15@136.243.104.103:24856,77ce4b0a96b3c3d6eb2beb755f9f6f573c1b4912@178.18.251.146:22656,48b00df4cd638e60c27eb2d69935692aaea93925@213.133.100.206:26656,5660a533218eed9dbbc569f38e6bc44666b1eb17@65.21.10.105:26656,a6591002cea520b51d5037646d7b597e8fc42be4@88.99.254.62:23656,5a6f8c5462221e6144ebf65c18a279398b2262e6@88.99.149.170:21656,61cc08b34ff0b05b766701129e468ec852ff4469@65.108.193.26:25656,51010331020f17e7c31c5bd7cf4d464e082fb1fa@78.46.19.116:29156,e7971b0ba715c6f00ec87f32596af5313ad3be3b@47.236.84.154:22656,298743e0b4813ada523e26922d335a3fb37ec58a@37.27.195.219:26656,e4a1365ebec482e10b3159167315324ed97ed318@65.109.30.35:24856,f3a0085a5d6009c5bd1e1348319fb71b2a5d749b@116.202.218.189:11856,f0dada36a2c1f1cfafc793e4842634f319b42002@65.109.197.53:24856,7166ca1097c67404faf3495cd83a33c604dba066@65.109.154.182:29656,71ccd5c9a6bf7687e95685416139dcfaacfbe2fb@65.21.47.4:13656,0426f506882d3d95308c320ad93ec0a4c8034afd@185.100.232.210:12656,2eb4d39dcafda73f5992d3ed23c1ed5a413e71da@15.235.216.26:10756,456b67c0e08aee93c838a243f6539d48019fb6a4@185.8.107.232:28656,8f6e03ed5bcc6fba0068e59a890a02827c747c24@84.247.142.192:26656,2d80f81c74e892a83075432d84a09e1bdf30e4d0@144.217.68.182:14556,0a66a8b150825b71d6655faaae2e2c8b811cd55e@148.113.170.13:16611,f466fd39e865e1625442c8e900df82a923fc88d0@149.202.79.19:12656,9a1633062b83e6ed06933d989a2ccf62617d7a4f@109.199.98.25:26656,d4a38805a7471916889e2a8c06fe5d02dac65095@185.187.170.170:26656,9d4ee7dea344cc5ca83215cf7bf69ba4001a6c55@5.9.73.170:29156,f06601c74a7d3e2db9031ae7519436bf0338070f@94.136.188.196:26656,87817c5c18f74c56f6b9c26eed3838460cb8b2a2@38.45.71.89:51556,6f2fbd9971c21eabe2c883c107ad2b7c48e1e3ad@116.202.32.209:45656,3f58299f67b94e158945b8f15033a409f24cd577@37.27.71.199:25656,8e5d8b822c67b9e60f884857c2b58af95e1c67be@152.53.19.64:14656,2af2680517f752ea824fa2cdc7dd0d75b250ea09@93.190.138.235:26556,8fc13eb23bb09225d08b4da9bb80ab3b2c008990@188.40.73.112:23656,7d1cf0710843670635a8e9c57f68fcf8a8809918@152.53.32.129:45656,ba2d34b423c40d4756bd88bb9a892914c0550d96@45.14.194.115:26656,ffe3c2be715ea500389954f66c63d1b589d5fc8f@195.3.220.4:45656,e1bbb487ef8a461ae7ec152406cb5498f66dc3b5@149.50.96.153:24856,28db1494d27da36208b2ad2ecb58b53a312a39c8@185.192.97.28:24856,be8a596a934e152aa5cdf52179f115341425860c@95.216.7.84:37656,f3c40275b0e198bef1c79111a04d0fed572a44da@94.72.100.234:45656,b9b94d2acb2d941afc016fe9baee62f3bd75a988@89.109.112.42:40656,ae0d22ad117322c7eb802d6001cdd85691026b6c@149.102.148.47:51556,e30f14cdfb4f5da2945f5b7b3e0f3fced899f1fa@51.75.144.149:24856,12993d8fecc2e6bb8543600c4784fabd1fc296fd@65.108.129.49:38656,247a415a06766df181c30328d30a89aeb8822afe@167.235.102.45:11856,635ffb879715272d54870fd9e6af5743212004c6@95.217.76.186:26656,710976805e0c3069662e63b9f244db68654e2f15@65.109.93.124:29256,021151a58f1cb3525cf2fc182d9443eb09e81181@65.109.83.40:29256,bbf8ef70a32c3248a30ab10b2bff399e73c6e03c@65.21.198.100:24856,328ab51e6c5d486135a46b251b206c16b586e0f4@156.67.80.21:24856,d2815e4cc08ff22fa25e00a9ba475aca03554456@84.247.131.187:24856,b6d2eb42e3ccab89748be31eaa2c36426e3a7465@65.108.109.48:16656,67ac5a45b0f8762d02b6c373e73dfc2f472028a9@149.102.157.11:45656,ce4413d495cae6c8e85235de695c0329071a267b@84.247.160.207:38656,d0291fff59890624630e4224bc6c48b2471914e8@44.198.174.33:26656,9da0f5b25a1ec1bacc76c834b4f3ab7d3d6bf4cb@195.26.245.117:45656,a4d4fc2d8e0b127d11f5c106ab241c475712d3f5@65.109.117.113:29256,39c6c221bc4914b5e0d1d1755c71c0700bcea933@142.93.9.189:26656,600e2360ea3c053e355027564da17f4e9caa1282@38.242.203.252:45656,764d29fcc58b1f1d806e0c192bc2c39cadef784d@95.217.225.147:45656,53fa02c9e9f3ad964c78fcf498d39ad245af3cd0@84.247.178.183:45656,8d7f28b530166672ee403760f3129cf1747bb43e@84.247.185.140:25656,ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@176.9.82.221:29156,6f6a3a908634b79b6fe7c4988efec2553f188234@103.184.192.245:29156
,247a415a06766df181c30328d30a89aeb8822afe@rpc.symphony-t.indonode.net:11856,adc09b9238bc582916abda954b081220d6f9cbc2@34.172.132.224:26656,eea2dc7e9abfd18787d4cc2c728689ad658cd3a2@35.184.9.159:26656,785f5e73e26623214269909c0be2df3f767fbe50@35.225.73.240:26656,8df964c61393d33d11f7c821aba1a72f428c0d24@34.41.129.120:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.symphonyd/config/config.toml
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025note\"/" $HOME/.symphonyd/config/app.toml
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.symphonyd/config/config.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.symphonyd/config/config.toml


# Config Pruning
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.symphonyd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.symphonyd/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.symphonyd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.symphonyd/config/app.toml


# Set Custom Port
sed -i.bak -e "s%:1317%:${SYMPHONY_PORT}317%g;
s%:8080%:${SYMPHONY_PORT}080%g;
s%:9090%:${SYMPHONY_PORT}090%g;
s%:9091%:${SYMPHONY_PORT}091%g;
s%:8545%:${SYMPHONY_PORT}545%g;
s%:8546%:${SYMPHONY_PORT}546%g;
s%:6065%:${SYMPHONY_PORT}065%g" $HOME/.symphonyd/config/app.toml
sed -i.bak -e "s%:26658%:${SYMPHONY_PORT}658%g;
s%:26657%:${SYMPHONY_PORT}657%g;
s%:6060%:${SYMPHONY_PORT}060%g;
s%:26656%:${SYMPHONY_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${SYMPHONY_PORT}656\"%;
s%:26660%:${SYMPHONY_PORT}660%g" $HOME/.symphonyd/config/config.toml
sed -i \
  -e 's|^chain-id *=.*|chain-id = "symphony-testnet-2"|' \
  -e 's|^keyring-backend *=.*|keyring-backend = "test"|' \
  -e 's|^node *=.*|node = "tcp://localhost:15657"|' \
  $HOME/.symphonyd/config/client.toml
```

Copy

```
# Download latest chain snapshot
symphonyd tendermint unsafe-reset-all --home ~/.symphonyd/ --keep-addr-book
curl https://files.nodesync.top/Symphony/symphony.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.symphonyd
```

### 5. Create Service and Start node <a href="#id-5.-create-service-and-start-node" id="id-5.-create-service-and-start-node"></a>

Copy

```
# Set Service File
sudo tee /etc/systemd/system/symphonyd.service > /dev/null <<EOF
[Unit]
Description=symphony-testnet
After=network-online.target

[Service]
User=$USER
ExecStart=$(which symphonyd) start --home $HOME/.symphonyd
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

Copy

```
sudo systemctl daemon-reload
sudo systemctl enable symphonyd
sudo systemctl restart symphonyd
sudo journalctl -u symphonyd -f --no-hostname -o cat
```

### 6. Wallet <a href="#id-6.-wallet" id="id-6.-wallet"></a>

Add New Wallet Key - **Save seed**

```bash
symphonyd keys add wallet
```

Recover existing key

```bash
symphonyd keys add wallet --recover
```

List All Keys

```bash
symphonyd keys list
```

#### 6.1 Query Wallet Balance <a href="#id-6.1-query-wallet-balance" id="id-6.1-query-wallet-balance"></a>

```bash
symphonyd q bank balances $(symphonyd keys show wallet -a)
```

#### 6.2 Check sync status <a href="#id-6.2-check-sync-status" id="id-6.2-check-sync-status"></a>

**False is synced**

```bash
symphonyd status 2>&1 | jq .SyncInfo.catching_up
```

#### 6.3 Create Validator <a href="#id-6.3-create-validator" id="id-6.3-create-validator"></a>

Change your info

```bash
symphonyd tx staking create-validator \
--amount=1000000note \
--pubkey=$(symphonyd tendermint show-validator) \
--moniker=$MONIKER \
--identity="YOUR_KEYBASE_ID" \
--details="YOUR_DETAILS" \
--website="YOUR_WEBSITE_URL" \
--chain-id=symphony-testnet-2 \
--commission-rate=0.10 \
--commission-max-rate=0.20 \
--commission-max-change-rate=0.01 \
--min-self-delegation=1000 \
--from=wallet \
--gas-adjustment=1.5 \
--gas="auto" \
--gas-prices=1note \
-y
```

#### 6.4 Edit Existing Validator <a href="#id-6.4-edit-existing-validator" id="id-6.4-edit-existing-validator"></a>

Change your info

```bash
symphonyd tx staking edit-validator \
--new-moniker="YOUR MONIKER" \
--identity="IDENTITY KEYBASE" \
--details="DETAILS VALIDATOR" \
--website="LINK WEBSITE" \
--chain-id=symphony-testnet-2 \
--from=wallet \
--gas-adjustment=1.5 \
--gas="auto" \
--gas-prices=1note \
-y
```

#### 6.5 Delegate Token to your own validator <a href="#id-6.5-delegate-token-to-your-own-validator" id="id-6.5-delegate-token-to-your-own-validator"></a>

```
symphonyd tx staking delegate $(symphonyd keys show wallet --bech val -a) 100000note --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.5 --gas auto --gas-prices 1note 
```

#### 6.6 Withdraw rewards and commission from your validator <a href="#id-6.6-withdraw-rewards-and-commission-from-your-validator" id="id-6.6-withdraw-rewards-and-commission-from-your-validator"></a>

```bash
symphonyd tx distribution withdraw-all-rewards --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.5 --gas auto --gas-prices 1note 
```

**6.6.1 Withdraw rewards and commission from your validator**

```bash
symphonyd tx distribution withdraw-rewards symphonyvaloper19k9qjyj00aadm45llngf95z5v73hggz4ae3w5n --from wallet --commission --chain-id symphony-testnet-2 --gas-adjustment 1.5 --gas auto --gas-prices 1note 
```

#### 6.7 Unjail validator <a href="#id-6.7-unjail-validator" id="id-6.7-unjail-validator"></a>

```bash
symphonyd tx slashing unjail --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.5 --gas auto --gas-prices 1note 
```

#### 6.8 Send Token to another wallet <a href="#id-6.8-send-token-to-another-wallet" id="id-6.8-send-token-to-another-wallet"></a>

```bash
symphonyd tx bank send wallet <TO_WALLET_ADDRESS> 10000note --from wallet --chain-id  symphony-testnet-2 --gas-adjustment 1.5 --gas auto --gas-prices 1note 
```

#### 6.9 Services Management <a href="#id-6.9-services-management" id="id-6.9-services-management"></a>

```bash
# Reload Service
sudo systemctl daemon-reload

# Enable Service
sudo systemctl enable symphonyd

# Disable Service
sudo systemctl disable symphonyd

# Start Service
sudo systemctl start symphonyd

# Stop Service
sudo systemctl stop symphonyd

# Restart Service
sudo systemctl restart symphonyd

# Check Service Status
sudo systemctl status symphonyd

# Check Service Logs
sudo journalctl -u symphonyd -f --no-hostname -o cat
```

### 7. Backup Validator <a href="#id-7.-backup-validator" id="id-7.-backup-validator"></a>

**Important**

```
cat $HOME/.symphonyd/config/priv_validator_key.json
```

### 8. Snapshot <a href="#id-8.-snapshot" id="id-8.-snapshot"></a>

```
sudo systemctl stop symphonyd

cp $HOME/.symphonyd/data/priv_validator_state.json $HOME/.symphonyd/priv_validator_state.json.backup
symphonyd tendermint unsafe-reset-all --home ~/.symphonyd/ --keep-addr-book

curl https://files.nodesync.top/Symphony/symphony.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.symphonyd

mv $HOME/.symphonyd/priv_validator_state.json.backup $HOME/.symphonyd/data/priv_validator_state.json

sudo systemctl restart symphonyd && sudo journalctl -fu symphonyd -o cat
```

### 9. Remove node <a href="#id-9.-remove-node" id="id-9.-remove-node"></a>

```
cd $HOME
sudo systemctl stop symphonyd.service
sudo systemctl disable symphonyd.service
sudo rm /etc/systemd/system/symphonyd.service
sudo systemctl daemon-reload
rm -f $(which symphonyd)
rm -rf $HOME/.symphonyd
rm -rf $HOME/symphonyd
```
