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
echo "export STRUCTS_CHAIN_ID="structstestnet-100"" >> $HOME/.bash_profile
echo "export STRUCTS_PORT="25"" >> $HOME/.bash_profile
source $HOME/.bash_profile


# Install Ignite
sudo curl https://get.ignite.com/cli! | bash
sudo mv ignite /usr/local/bin/
ignite version


# Download Binary
cd $HOME
git clone --branch v0.5.0-beta https://github.com/playstructs/structsd.git 
cd structsd
ignite chain build


# Config and Init App
structsd init $MONIKER --chain-id $STRUCTS_CHAIN_ID


# Add Genesis File and Addrbook
wget -O $HOME/.structs/config/genesis.json https://files.shazoe.xyz/testnets/structs/genesis.json
wget -O $HOME/.structs/config/addrbook.json https://files.shazoe.xyz/testnets/structs/addrbook.json


#Configure Seeds and Peers
SEEDS=""
PEERS="a32e2f9d8ec4bd56a1caed03a2fa8bbadbe75995@95.217.89.100:14456,3fba9d1c730954bd02edd712de244f2e97e5e13c@88.99.61.53:32656,fd3cc5f0769dea1b520c3d3eea230a2f196c5693@144.76.92.22:10656,f9ff152e331904924c26a4f8b1f46e859d574342@155.138.142.145:26656,197cfbe9f1c7bb8446a9e217d6e3710014bdc496@95.111.248.136:26656,372e686bc84528d9beccf15429f94846cd0f54d8@159.69.193.68:11656,8450315267be7073317c52432a1a8f7a94e039b8@192.155.91.61:26656,9b5164e4ae58f1a5e8f7a8681216dc79cf111aef@188.165.226.46:26696,09e8f5be4c58c0a8ddf5596742a2322431523f2f@216.128.181.240:26656,4d6a8ba29019e2af39910edad5665d8d91d46dde@65.21.32.216:60856"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.structs/config/config.toml
sed -i 's/minimum-gas-prices =.*/minimum-gas-prices = "0alpha"/g' $HOME/.structs/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.structs/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.structs/config/config.toml


# Config Pruning
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="19"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.structs/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.structs/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.structs/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.structs/config/app.toml


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
sudo systemctl start struct &#x26; sudo systemctl status struct &#x26;&#x26; \
sudo journalctl -u struct -f --no-hostname -o cat
</code></pre>
