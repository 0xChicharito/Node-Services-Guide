---
cover: ../../.gitbook/assets/1080x360 (1).jpg
coverY: 0
---

# ⚙️ Installation (Odyssey)

### System Specs

| Hardware  | Requirement |
| --------- | ----------- |
| CPU       | 4 Cores     |
| RAM       | 8 GB        |
| Disk      | 200 GB      |
| Bandwidth | 10 MBit/s   |

```bash
# install dependencies, if needed
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

```bash
# install go, if needed
cd $HOME
VER="1.22.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin

# set vars
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export STORY_CHAIN_ID="odyssey"" >> $HOME/.bash_profile
echo "export STORY_PORT="52"" >> $HOME/.bash_profile
source $HOME/.bash_profile

# download binaries
cd $HOME
wget -O geth https://github.com/piplabs/story-geth/releases/download/v0.10.0/geth-linux-amd64
chmod +x $HOME/geth
mv $HOME/geth ~/go/bin/
[ ! -d "$HOME/.story/story" ] && mkdir -p "$HOME/.story/story"
[ ! -d "$HOME/.story/geth" ] && mkdir -p "$HOME/.story/geth"

# install Story
cd $HOME
rm -rf story
git clone https://github.com/piplabs/story
cd story
git checkout v0.12.0
go build -o story ./client 
mv $HOME/story/story $HOME/go/bin/

# init story app
story init --moniker test --network odyssey

# set seeds and peers
SEEDS="434af9dae402ab9f1c8a8fc15eae2d68b5be3387@story-testnet-seed.itrocket.net:29656"
PEERS="c2a6cc9b3fa468624b2683b54790eb339db45cbf@story-testnet-peer.itrocket.net:26656,20b8296d4019cdc0b72773ea7984523af40feacb@148.72.138.4:26656,201ce716a03aa45a7e037575a996a5082348eb44@148.72.138.7:26656,ae07ef7d61f38ce3b96e95450c5d4776a1ce1d5e@148.72.138.6:26656,2086affe2a3ea6ba3a9e6ca16a3ba406906f6eea@141.98.217.151:26656,d5519e378247dfb61dfe90652d1fe3e2b3005a5b@213.239.207.162:26656,04e5734295da362f09a61dd0a9999449448a0e5c@52.14.39.177:26656,4d881b88285d41d2f6c33fdf50a307666aacaca1@65.109.36.231:52656,b04bae4f88ca12d45fc14be29ce96837b61a72b8@65.109.49.115:26656,e7b7a84d9b93df03d1843c288ca97f6d3e6a1045@15.235.225.237:16756,7ecb96bb4778b3f979be7c8e720cc9ee739ac770@104.198.43.15:26656,0f1b1840d2f3302b60a671be500b181eb354e21f@5.9.117.149:45656,c5c214377b438742523749bb43a2176ec9ec983c@176.9.54.69:26656,5dec0b793789d85c28b1619bffab30d5668039b7@150.136.113.152:26656,89a07021f98914fbac07aae9fbb12a92c5b6b781@152.53.102.226:26656,5c713a045806be88171d27b837ea4dbcd5dbf5c0@68.183.231.160:26656,81eaee3be00b21d0a124016b62fb7176fa05a4f9@185.198.49.133:33556,e83d15daaccdbe1aa2ac9446dabe9986cf6c67c2@152.53.66.0:26656,4f7931c2c795da73d7ea20f0166791f134f6e45e@2.59.117.67:26656,2df2b0b66f267939fea7fe098cfee696d6243cec@65.108.193.224:23656,bd58bf29180f476bd250af22d6026559d7eff289@146.59.118.198:26656,3d0dc5d0d61ed8eb4559708d6725fa27846754ba@65.108.75.179:11656,07ab4164e1d0ee17c565542856ac58981537156f@185.16.38.165:42656,434af9dae402ab9f1c8a8fc15eae2d68b5be3387@142.132.253.112:29656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.story/story/config/config.toml

# download genesis and addrbook
wget -O $HOME/.story/story/config/genesis.json https://server-3.itrocket.net/testnet/story/genesis.json
wget -O $HOME/.story/story/config/addrbook.json  https://server-3.itrocket.net/testnet/story/addrbook.json

# set custom ports in story.toml file
sed -i.bak -e "s%:1317%:${STORY_PORT}317%g;
s%:8551%:${STORY_PORT}551%g" $HOME/.story/story/config/story.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${STORY_PORT}658%g;
s%:26657%:${STORY_PORT}657%g;
s%:26656%:${STORY_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${STORY_PORT}656\"%;
s%:26660%:${STORY_PORT}660%g" $HOME/.story/story/config/config.toml

# enable prometheus and disable indexing
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.story/story/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.story/story/config/config.toml

# create geth servie file
sudo tee /etc/systemd/system/story-geth.service > /dev/null <<EOF
[Unit]
Description=Story Geth daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$HOME/go/bin/geth --odyssey --syncmode full --http --http.api eth,net,web3,engine --http.vhosts '*' --http.addr 0.0.0.0 --http.port ${STORY_PORT}545 --authrpc.port ${STORY_PORT}551 --ws --ws.api eth,web3,net,txpool --ws.addr 0.0.0.0 --ws.port ${STORY_PORT}546
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

# create story service file
sudo tee /etc/systemd/system/story.service > /dev/null <<EOF
[Unit]
Description=Story Service
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/.story/story
ExecStart=$(which story) run

Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# download snapshots
# backup priv_validator_state.json
cp $HOME/.story/story/data/priv_validator_state.json $HOME/.story/story/priv_validator_state.json.backup

# remove old data and unpack Story snapshot
rm -rf $HOME/.story/story/data
curl https://snapshot.node9x.com/story_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.story/story

# restore priv_validator_state.json
mv $HOME/.story/story/priv_validator_state.json.backup $HOME/.story/story/data/priv_validator_state.json

# delete geth data and unpack Geth snapshot
rm -rf $HOME/.story/geth/odyssey/geth/chaindata
mkdir -p $HOME/.story/geth/odyssey/geth
curl https://server-3.itrocket.net/testnet/story/geth_story_2024-11-08_400505_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.story/geth/odyssey/geth

# enable and start geth, story
sudo systemctl daemon-reload
sudo systemctl enable story story-geth
sudo systemctl restart story-geth && sleep 5 && sudo systemctl restart story

# check logs
journalctl -u story -u story-geth -f
```

### Create validator <a href="#create-validator" id="create-validator"></a>

View your validator key

```bash
story validator export
```

Export EVM private key

```bash
story validator export --export-evm-key
```

View EVM private key and make a key backup

```bash
cat $HOME/.story/story/config/private_key.txt
```

Faucet link: [https://odyssey.faucet.story.foundation/](https://odyssey.faucet.story.foundation/)

Storyscan: [https://odyssey-testnet-explorer.storyscan.xyz/](https://odyssey-testnet-explorer.storyscan.xyz/)

#### Before creating a validator, wait for your node to get fully synced. Once "catching\_up" is "false", move on to the next step

```bash
curl localhost:$(sed -n '/\[rpc\]/,/laddr/ { /laddr/ {s/.*://; s/".*//; p} }' $HOME/.story/story/config/config.toml)/status | jq
```

#### Create validator

```bash
story validator create --stake 1000000000000000000 --private-key "your_private_key"
```

**Validator staking**

```bash
story validator stake \
   --validator-pubkey "VALIDATOR_PUB_KEY_IN_BASE64" \
   --stake 1000000000000000000 \
   --private-key xxxxxxxxxxxxxx
```

### Remember to backup your validator priv\_key from here:

```bash
cat $HOME/.story/story/config/priv_validator_key.json
```

### Delete node <a href="#delete-node" id="delete-node"></a>

```bash
sudo systemctl stop story story-geth
sudo systemctl disable story story-geth
rm -rf $HOME/.story
sudo rm /etc/systemd/system/story.service /etc/systemd/system/story-geth.service
sudo systemctl daemon-reload
```
