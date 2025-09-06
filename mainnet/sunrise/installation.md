# ⚙️ Installation

```shell
sudo apt update
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```

\
Install Go

```shell
# Install Go
cd $HOME
curl https://dl.google.com/go/go1.22.5.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
  
# Update environment variables to include go
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
  
source $HOME/.profile
  
# check go version
go version
```

\
Install the Node

```shell
# Install Sunrise node
cd $HOME
git clone https://github.com/sunriselayer/sunrise sunrise
cd sunrise
git checkout v1.1.0
make install
cd $HOME
sunrised version
```

Or use CLI

```bash
cd $HOME
wget -O sunrised https://github.com/sunriselayer/sunrise/releases/download/v1.1.0/sunrised-linux-amd64
chmod +x $HOME/sunrised
mv $HOME/sunrised $HOME/go/bin/sunrise
```

\
Initialize the Node

```shell
# Initialize Node
sunrised init <moniker> --chain-id=sunrise-1
sed -i "s/chain-id = .*/chain-id = \"sunrise-1\"/" $HOME/.sunrise/config/client.toml
sed -i "s/keyring-backend = .*/keyring-backend = \"os\"/" $HOME/.sunrise/config/client.toml
sed -i "s/node = .*/node = \"tcp:\/\/localhost:${SUNRISE_PORT}657\"/" $HOME/.sunrise/config/client.toml
```

```bash
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.sunrise/config/app.toml 
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.sunrise/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.sunrise/config/app.toml
```

Pruning:

```bash
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.sunrise/config/config.toml
```

Set indexer:

```bash
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.0025urise"|g' $HOME/.sunrise/config/app.toml
```

Set min gas:

```bash
sed -i.bak -e "s%:1317%:${SUNRISE_PORT}317%g;
s%:8080%:${SUNRISE_PORT}080%g;
s%:9090%:${SUNRISE_PORT}090%g;
s%:9091%:${SUNRISE_PORT}091%g;
s%:8545%:${SUNRISE_PORT}545%g;
s%:8546%:${SUNRISE_PORT}546%g;
s%:6065%:${SUNRISE_PORT}065%g" $HOME/.sunrise/config/app.toml
sed -i.bak -e "s%:26658%:${SUNRISE_PORT}658%g;
s%:26657%:${SUNRISE_PORT}657%g;
s%:6060%:${SUNRISE_PORT}060%g;
s%:26656%:${SUNRISE_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${SUNRISE_PORT}656\"%;
s%:26660%:${SUNRISE_PORT}660%g" $HOME/.sunrise/config/config.toml
```

\
Download Genesis file

```shell
curl -Ls https://support.synergynodes.com/genesis/sunrise_mainnet/genesis.json > $HOME/.sunrise/config/genesis.json
```

\
Download Addrbook file

```shell
curl -Ls https://support.synergynodes.com/addrbook/sunrise_mainnet/addrbook.json > $HOME/.sunrise/config/addrbook.json
```

\
Add / Update Persistent Peers

```shell
# Add / Update Peers
PEERS=da0f328712a72cff3e472c43ae3fa4020f9d420b@52.68.80.18:26656,7db7f656d36c420f39a8eab76c50c41cff440fa9@65.109.58.158:28356,a54886fba0cf118825d616e0caf9d52c534d7042@82.22.184.97:26656,6321f379be8a43731c15726c979e4993de433e14@65.21.130.53:26656,9f83856d9d5a2137bddbda57c47bb68028a2f1da@149.86.227.136:26656,1797481e0cd2794c8d0f3a92bb00859887cd60c5@103.241.50.78:26656,bb69adc6246d31899055c2da852ef5c3fd5bbfe3@51.195.60.23:28356,1d59382e2a6db1f80ca90b7cf1df1fcada237fab@65.108.68.53:26670,211e3de05bec1deb2d641a4855968f02044379eb@23.88.67.245:46656,13b3e44f5164b7e85871e9b58f262ca1097b1653@158.69.125.73:11556,49be16d94c586f3ebd15f7cc7174d56765043b11@64.185.226.202:28356,9e3fdd30b550024f09df85d6e7f9a3c3daa32b36@34.87.34.133:26656,34447c658f69fa1bc56125e991c207da1efbf137@65.109.59.22:28356,2494005ba072167d29e6f55a9b378a781872a49e@65.109.145.247:26656,8b4136343917f704c81c8e49618a41472d8e1abe@65.108.229.19:26776,50c3a76591c4827ef97968a742f9450b19e474b6@128.140.68.218:26656,da1b772776bcc3c84e21d7aa72314f58c2729a43@65.109.18.169:36156,fc93745fb6d05955f5d3e107d495c69d7d545828@91.99.196.242:26656,2a30964e07118a0cb03bb1cae9185d37a967230a@207.148.68.35:26656,2404dca4d4b0831e69dd010539f0c391bcd0523a@95.217.128.50:26656,12b53dc2769314124f7b2cad15645032a0d06417@188.245.248.134:26656,34e39405f02872a4a9403f241066cf0875a66ce2@65.108.7.249:28356,e776df4c573785a3416da430fb9c90be72ea795e@23.129.20.120:28356,3b02bfd265b4b67d867c0da47f085dd626100569@65.21.132.31:21656,97ae5714afec408cb6f5fb78cf830af8c5d7b535@65.109.104.17:26656,41e61c0441f0086caf17186950fc440d45870bf5@198.13.54.102:26656,8b13908c44911f9797798e951557c17ef2490ee8@95.217.203.185:26656,5bc75c4efd94656d5f6e36d0f742bb2b3fef5f35@95.217.141.114:36156,345a34c3e1b6c82af4e85fafa03d27980332dac7@37.252.186.241:26656,13c1a5edd2e09c8ec3998fdc2c8ede330c6224bf@65.108.204.225:28356,2d712853b8aeca55161a71f1f5ca8bb27cc499d2@38.146.3.231:28356,9dcd02770449ff5a92daa059890d923ac9882ab2@168.119.143.51:21656,68db7a302272b8d0497d4d857b75661a69310079@149.28.143.138:26656,60a83f51c20d39b7e69594f538513a80521eb0e8@45.32.30.169:26656,6f0c9da8bb41b81d471d777e7ab88d816e8938b3@154.26.136.5:26656,9f8312a2f9cf96017d17acc55216bd813111a6ed@45.250.252.75:56326,7e8cf0f634173b2db50bae901f4de8d6af0bac3b@54.179.30.77:26656,a2995719a5a67675d9e568f222f1672f438e97d6@15.235.225.243:56326
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = "$PEERS"/" $HOME/.sunrise/config/config.toml
```

\
Download & decompress Snapshot

```shell
# Follow these steps
cd $HOME
sudo systemctl stop sunrised
cp $HOME/.sunrise/data/priv_validator_state.json $HOME/.sunrise/priv_validator_state.json.backup
rm -rf $HOME/.sunrise/data
wget -O sunrise_mainnet_494085.tar.lz4 https://support.synergynodes.com/snapshots/sunrise_mainnet/sunrise_mainnet_494085.tar.lz4
lz4 -c -d sunrise_mainnet_494085.tar.lz4 | tar -x -C $HOME/.sunrise
mv $HOME/.sunrise/priv_validator_state.json.backup $HOME/.sunrise/data/priv_validator_state.json
sudo systemctl restart sunrised
```

\
Create Service File

```shell
# Create Service
sudo tee /etc/systemd/system/sunrised.service > /dev/null <<EOF
[Unit]
Description=sunrised Daemon
#After=network.target
StartLimitInterval=350
StartLimitBurst=10

[Service]
Type=simple
User=root
ExecStart=$(which sunrised) start
Restart=on-abort
RestartSec=30

[Install]
WantedBy=multi-user.target

[Service]
LimitNOFILE=1048576
EOF
```

\
Start the Node

```shell
# Enable service
sudo systemctl enable sunrised

# Start service
sudo service sunrised start

# Check logs
sudo journalctl -fu sunrised
```
