# ⚙️ Installation

### Manual Installation <a href="#manual-installation" id="manual-installation"></a>

<pre class="language-bash"><code class="lang-bash"># Update &#x26; install dependencies
sudo apt update &#x26;&#x26; sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq htop tmux chrony liblz4-tool -y


# Install Go
cd $HOME
VER="1.22.2"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
3sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] &#x26;&#x26; touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] &#x26;&#x26; mkdir -p ~/go/bin
go version

<strong>#PORT=25
</strong># Set Vars
<strong>MONIKER=&#x3C;YOUR_MONIKER_NAME_GOES_HERE>
</strong>echo "export MONIKER=$MONIKER" >> $HOME/.bash_profile
echo "export STRUCTS_CHAIN_ID="structstestnet-101"" >> $HOME/.bash_profile
echo "export STRUCTS_PORT="25"" >> $HOME/.bash_profile
source $HOME/.bash_profile


# Install Ignite
sudo curl https://get.ignite.com/cli! | bash
sudo mv ignite /usr/local/bin/
ignite version


# Download Binary
cd $HOME
git clone https://github.com/playstructs/structsd.git
cd structsd
ignite chain build
structsd version --long | grep -e commit -e version


# Config and Init App
structsd init $MONIKER --chain-id $STRUCTS_CHAIN_ID


# Add Genesis File and Addrbook
curl -Ls https://snapshots.moonbridge.org/testnet/structs/genesis.json > $HOME/.structs/config/genesis.json
curl -Ls https://snapshots.moonbridge.org/testnet/structs/addrbook.json > $HOME/.structs/config/addrbook.json

#Configure Seeds and Peers
SEEDS=""
PEERS="f9ff152e331904924c26a4f8b1f46e859d574342@155.138.142.145:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.structs/config/config.toml

# Setting minimum gas price
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0ualpha\"|" $HOME/.structs/config/app.toml

# Setting pruning
sed -i -e 's|^pruning *=.*|pruning = "custom"|' $HOME/.structs/config/app.toml
sed -i -e 's|^pruning-keep-recent  *=.*|pruning-keep-recent = "100"|' $HOME/.structs/config/app.toml
sed -i -e 's|^pruning-interval *=.*|pruning-interval = "10"|' $HOME/.structs/config/app.toml

# Disable indexer
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.structs/config/config.toml

# Enable Prometheus
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.structs/config/config.toml

# Set Custom Port
sed -i.bak -e "s%:1317%:${STRUCTS_PORT}317%g;
s%:8080%:${STRUCTS_PORT}080%g;
s%:9090%:${STRUCTS_PORT}090%g;
s%:9091%:${STRUCTS_PORT}091%g;
s%:8545%:${STRUCTS_PORT}545%g;
s%:8546%:${STRUCTS_PORT}546%g;
s%:6065%:${STRUCTS_PORT}065%g" $HOME/.structs/config/app.toml
sed -i.bak -e "s%:26658%:${STRUCTS_PORT}658%g;
s%:26657%:${STRUCTS_PORT}657%g;
s%:6060%:${STRUCTS_PORT}060%g;
s%:26656%:${STRUCTS_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${STRUCTS_PORT}656\"%;
s%:26660%:${STRUCTS_PORT}660%g" $HOME/.structs/config/config.toml
sed -i \
  -e 's|^chain-id *=.*|chain-id = "structstestnet-100"|' \
  -e 's|^node *=.*|node = "tcp://localhost:25657"|' \
  $HOME/.structs/config/client.toml


# Set Service File
sudo tee /etc/systemd/system/structsd.service > /dev/null &#x3C;&#x3C;EOF
[Unit]
Description=structs-testnet
After=network-online.target

[Service]
User=$USER
ExecStart=$(which structsd) start  --home $HOME/.structs
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF


# Enable and Start Service
sudo systemctl daemon-reload &#x26;&#x26; \
sudo systemctl enable structsd &#x26;&#x26; \
sudo systemctl start structsd &#x26; sudo systemctl status structsd &#x26;&#x26; \
sudo journalctl -u structsd -f --no-hostname -o cat
</code></pre>
