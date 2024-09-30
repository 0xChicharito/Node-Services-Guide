---
description: 'Recommended Hardware: 8 Cores, 64GB RAM, 1 TB of storage (NVME)'
---

# Installation

```bash
# install dependencies, if needed
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y

```

```bash
# install go, if needed
cd $HOME
VER="1.21.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin

# set vars
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export OG_CHAIN_ID="zgtendermint_16600-2"" >> $HOME/.bash_profile
echo "export OG_PORT="47"" >> $HOME/.bash_profile
source $HOME/.bash_profile

# download binary
cd $HOME
rm -rf 0g-chain
git clone -b v0.2.5 https://github.com/0glabs/0g-chain.git
cd 0g-chain
make install

# config and init app
0gchaind config node tcp://localhost:${OG_PORT}657
0gchaind config keyring-backend os
0gchaind config chain-id zgtendermint_16600-2
0gchaind init "test" --chain-id zgtendermint_16600-2

# download genesis and addrbook
wget -O $HOME/.0gchain/config/genesis.json https://testnet-files.itrocket.net/og/genesis.json
wget -O $HOME/.0gchain/config/addrbook.json https://testnet-files.itrocket.net/og/addrbook.json

# set seeds and peers
SEEDS="8f21742ea5487da6e0697ba7d7b36961d3599567@og-testnet-seed.itrocket.net:47656"
PEERS="c76473c97fa718d1c4c48910c17318883300a36b@og-testnet-peer.itrocket.net:11656,055e3e65fd72102f389372564e0107e3ee5022fa@167.86.95.218:12656,2de20431412255201b960a0713c3a3f6fdbeb7e7@173.249.19.219:12656,1c9a00178d730a35f9a5ca3329f7bd612d6f8687@65.109.50.163:11656,f5f3dcf871fe72ff2e75c4ca0979969f2a1a6ea5@95.214.55.6:46656,7a941518231c253c16efa1f7f1a2fe1905715508@198.7.115.183:656,4e16115a559693c3af81da2d221b280d0fbf9679@194.163.151.186:12656,430b61d17e76c4b5ebdab8487efc28c4c869843f@89.117.53.249:12656,1754dac0846c42ebe21fe1935eda0311d567d6a9@45.14.194.144:12656,85f1a5c5e62bbe59d9764453bf4624dc261a53f7@38.242.237.56:12656,5b3202f4ee36451778646317ae569df1513fdbb2@38.242.230.75:12656,d04a1d38f5fda5531eb65635a7d537248c7761e1@116.202.39.234:16656,6ea4a3942152a33a50c54cc60aa311fd43cc71d7@144.91.93.99:12656,314993bcb6d20841708fc10c6cabc09fcd98893f@89.116.31.3:12656,881b2297ac90fdf6803136101c1b33eeb52a0bcc@213.199.37.74:12656,a3605733629ed17c8288ed26c4a6b53fc6698fe1@37.60.253.50:12656,2d780e7cae16cf25dfb992e294d5f672ae8a65ac@185.190.140.189:12656,5c64398081c1330d94bc1c8ef8105054d00adb69@144.76.63.246:26656,36a02574f529d621fe60e500d92a0e651a5b9d0b@155.133.22.157:12656,7e49c7c5d8cf1a4f79d3a2c4a2c3597d144e638e@156.67.81.135:12656,1e73e6eaba9d8d75596f19f688569674894dc2ba@161.97.69.60:12656,85eec3750270e50ea73c46b1caa72e7110fa7b1b@156.67.81.129:12656,90490155eb1e28a00cb9000657ef53cf9822e9e2@185.245.182.248:12656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.0gchain/config/config.toml


# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${OG_PORT}317%g;
s%:8080%:${OG_PORT}080%g;
s%:9090%:${OG_PORT}090%g;
s%:9091%:${OG_PORT}091%g;
s%:8545%:${OG_PORT}545%g;
s%:8546%:${OG_PORT}546%g;
s%:6065%:${OG_PORT}065%g" $HOME/.0gchain/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${OG_PORT}658%g;
s%:26657%:${OG_PORT}657%g;
s%:6060%:${OG_PORT}060%g;
s%:26656%:${OG_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${OG_PORT}656\"%;
s%:26660%:${OG_PORT}660%g" $HOME/.0gchain/config/config.toml

# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.0gchain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.0gchain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.0gchain/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0ua0gi"|g' $HOME/.0gchain/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.0gchain/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.0gchain/config/config.toml

# create service file
sudo tee /etc/systemd/system/0gchaind.service > /dev/null <<EOF
[Unit]
Description=0G node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.0gchain
ExecStart=$(which 0gchaind) start --home $HOME/.0gchain
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# reset and download snapshot
0gchaind tendermint unsafe-reset-all --home $HOME/.0gchain
if curl -s --head curl https://testnet-files.itrocket.net/og/snap_og.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://testnet-files.itrocket.net/og/snap_og.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.0gchain
    else
  echo no have snap
fi

# enable and start service
sudo systemctl daemon-reload
sudo systemctl enable 0gchaind
sudo systemctl restart 0gchaind && sudo journalctl -u 0gchaind -f
```

Or below command if you don't see the logs

```bash
tail -f /root/.0gchain/log/chain.log
```

### Create wallet <a href="#create-wallet" id="create-wallet"></a>

```bash
# to create a new wallet, use the following command. don’t forget to save the mnemonic
0gchaind keys add $WALLET

# to restore exexuting wallet, use the following command
0gchaind keys add $WALLET --recover

# save wallet and validator address
WALLET_ADDRESS=$(0gchaind keys show $WALLET -a)
VALOPER_ADDRESS=$(0gchaind keys show $WALLET --bech val -a)
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile

# check sync status, once your node is fully synced, the output from above will print "false"
0gchaind status 2>&1 | jq 

# before creating a validator, you need to fund your wallet and check balance
0gchaind query bank balances $WALLET_ADDRESS 
```

### Create validator <a href="#create-validator" id="create-validator"></a>

```bash
0gchaind tx staking create-validator \
--amount 1000000ua0gi \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(0gchaind tendermint show-validator) \
--moniker "test" \
--identity "" \
--website "" \
--details "I love blockchain ❤️" \
--chain-id zgtendermint_16600-2 \
--gas=auto --gas-adjustment=1.6 \
-y
```

### Monitoring <a href="#monitoring" id="monitoring"></a>

If you want to have set up a monitoring and alert system use [our cosmos nodes monitoring guide with tenderduty](https://teletype.in/@itrocket/bdJAHvC\_q8h)

### Security <a href="#security" id="security"></a>

To protect you keys please don\`t share your privkey, mnemonic and follow basic security rules

#### Set up ssh keys for authentication <a href="#ssh" id="ssh"></a>

You can use this [guide](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04) to configure ssh authentication and disable password authentication on your server

#### Firewall security <a href="#firewall" id="firewall"></a>

Set the default to allow outgoing connections, deny all incoming, allow ssh and node p2p port

```bash
sudo ufw default allow outgoing 
sudo ufw default deny incoming 
sudo ufw allow ssh/tcp 
sudo ufw allow ${OG_PORT}656/tcp
sudo ufw enable
```

### Delete node <a href="#delete" id="delete"></a>

```bash
sudo systemctl stop 0gchaind
sudo systemctl disable 0gchaind
sudo rm -rf /etc/systemd/system/0gchaind.service
sudo rm $(which 0gchaind)
sudo rm -rf $HOME/.0gchain
sed -i "/OG_/d" $HOME/.bash_profile
```
