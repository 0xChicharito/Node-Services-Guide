# Installation

#### Install Dependencies <a href="#install-dependencies" id="install-dependencies"></a>

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install curl unzip clang pkg-config libssl-dev jq build-essential tar wget  bsdmainutils git make ncdu gcc git jq htop tmux chrony liblz4-tool fail2ban -y
```

#### Install GO <a href="#install-go" id="install-go"></a>

```bash
cd $HOME
VER="1.23.8"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
go version
```

#### Download and Build Binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```bash
cd $HOME
git clone https://github.com/gnolang/gno.git
cd gno
git checkout chain/test6
make -C gno.land install.gnoland && make -C contribs/gnogenesis install && make install_gnokey
gnoland --help
```

#### Config and Init App <a href="#config-and-init-app" id="config-and-init-app"></a>

```bash
cd $HOME
gnoland secrets init && gnoland config init
gnoland config set rpc.laddr tcp://0.0.0.0:42657
gnoland config set p2p.laddr tcp://0.0.0.0:42656
gnoland config set moniker Your_Moniker
gnoland config set consensus.peer_gossip_sleep_duration 10ms
gnoland config set consensus.timeout_commit 3s
gnoland config set mempool.size 10000
gnoland config set p2p.flush_throttle_timeout 10ms
gnoland config set p2p.max_num_outbound_peers 40
gnoland config set p2p.persistent_peers g1yjduxd37l9ep4aw2yprs3pveklepwznhu3dd8y@gnoland-testnet-rpc.shazoes.xyz:42656,g1s0x78pl3c2xv2n7hp33lh4jkyqvhg5hlx6huh7@gno-core-sen-1.test6.testnets.gno.land:26656,g1jeta40dllwtrh293498hq0dh0cr3u4gw77h5rc@gno-core-sen-2.test6.testnets.gno.land:26656
gnoland config set p2p.seeds g1yjduxd37l9ep4aw2yprs3pveklepwznhu3dd8y@gnoland-testnet-rpc.shazoes.xyz:42656,g1s0x78pl3c2xv2n7hp33lh4jkyqvhg5hlx6huh7@gno-core-sen-1.test6.testnets.gno.land:26656,g1jeta40dllwtrh293498hq0dh0cr3u4gw77h5rc@gno-core-sen-2.test6.testnets.gno.land:26656
```

#### Download Genesis <a href="#download-genesis" id="download-genesis"></a>

```bash
wget -O $HOME/gnoland-data/config/genesis.json https://files.shazoes.xyz/testnets/gnoland/genesis.json
```

#### Set Service File <a href="#set-service-file" id="set-service-file"></a>

```bash
sudo tee /etc/systemd/system/gnoland.service > /dev/null <<EOF
[Unit]
Description=gnoland-testnet
After=network-online.target

[Service]
User=$USER
ExecStart=$(which gnoland) start --genesis $HOME/gnoland-data/config/genesis.json --data-dir $HOME/gnoland-data
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

#### Enable Service and Start Node <a href="#enable-service-and-start-node" id="enable-service-and-start-node"></a>

```bash
sudo systemctl daemon-reload && sudo systemctl enable gnoland && sudo systemctl start gnoland && sudo journalctl -fu gnoland -o cat
```

