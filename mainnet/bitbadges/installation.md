# ⚙️ Installation

### Installation <a href="#installation" id="installation"></a>

Install Go

```bash
wget https://go.dev/dl/go1.23.3.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.23.3.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
source ~/.profile
go version
```

Download binary file

```bash
git clone https://github.com/BitBadges/bitbadgeschain.git
cd bitbadgeschain
git checkout v12
make build-linux/amd64
cp build/bitbadgeschain-linux-amd64 /usr/local/bin/bitbadgeschaind
chmod +x /usr/local/bin/bitbadgeschaind
```

Download libwasm

```bash
mkdir -p $HOME/.bitbadgeschain/lib
wget https://github.com/BitBadges/bitbadgeschain/releases/download/v1.0-betanet/libwasmvm.x86_64.so
chmod +x libwasmvm.x86_64.so
mv libwasmvm.x86_64.so $HOME/.bitbadgeschain/lib
```

Set libswam environment

```bash
echo 'export LD_LIBRARY_PATH=/root/.bitbadgeschain/lib:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc
```

### Initialize the node <a href="#initialize-the-node" id="initialize-the-node"></a>

```bash
bitbadgeschaind init <your-name> --chain-id bitbadges-1
```

Download genesis and addrbook

```bash
curl -L https://snapshot-bitbadges.provewithryd.xyz/genesis.json > $HOME/.bitbadgeschain/config/genesis.json
curl -L https://snapshot-bitbadges.provewithryd.xyz/addrbook.json > $HOME/.bitbadgeschain/config/addrbook.json
```

Configure Seeds and Peers

```bash
PEERS=$(curl -sS https://rpc-bitbadges.provewithryd.xyz/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | sed -z 's|\n|,|g;s|,$||')
sed -i -e "s|^persistent_peers *=.*|persistent_peers = '$PEERS'|" $HOME/.bitbadgeschain/config/config.toml
```

Custumize Pruning

```bash
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.bitbadgeschain/config/app.toml 
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.bitbadgeschain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"20\"/" $HOME/.bitbadgeschain/config/app.toml
```

Set Minumum Gas & Disable Indexer

```bash
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.0001ubadge\"|" $HOME/.bitbadgeschain/config/app.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.bitbadgeschain/config/config.toml
```

### Create Systemd Services <a href="#create-systemd-services" id="create-systemd-services"></a>

```bash
tee /etc/systemd/system/bitbadgeschaind.service > /dev/null <<EOF
[Unit]
Description=BitBadges Mainnet Node
After=network-online.target

[Service]
User=root
ExecStart=/usr/local/bin/bitbadgeschaind start
Restart=always
RestartSec=3
LimitNOFILE=65535
Environment="LD_LIBRARY_PATH=/root/.bitbadgeschain/lib"

[Install]
WantedBy=multi-user.target
EOF
```

### Enable and Start Services <a href="#enable-and-start-services" id="enable-and-start-services"></a>

```bash
systemctl daemon-reexec
systemctl daemon-reload
systemctl enable bitbadgeschaind.service
systemctl start bitbadgeschaind.service
```
