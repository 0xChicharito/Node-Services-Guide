---
description: 'Recommended Hardware: 4 Cores, 16GB RAM, 1TB of storage (NVME)'
---

# ☑️ 0G Storage Node

### **System updates, installation of required dependencies** <a href="#system-updates-installation-of-required-dependencies" id="system-updates-installation-of-required-dependencies"></a>

```bash
sudo apt-get update
sudo apt-get install clang cmake build-essential
sudo apt install cargo
```

**Install go**

```bash
cd $HOME && \
ver="1.22.0" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile && \
source ~/.bash_profile && \
go version
```

**Install rust**

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustc --version
```

_When prompted choice of 1,2 and 3 just hit enter to continue_

**Build zgs\_node binary from source with rust**

```bash
rm -rf 0g-storage-node
git clone -b v0.7.3 https://github.com/0glabs/0g-storage-node.git
cd 0g-storage-node
git stash
git tag -d v0.4.4
git fetch --all --tags
git checkout 920efe0b597866759ba3d7b3e70e9f9d8e69bd2b
git submodule update --init
sudo apt install cargo
cargo build --release
```

**Set vars**

PLEASE INPUT YOUR OWN JSON-RPC ENDPOINT (VALIDATOR\_NODE\_IP:8545) OR YOU CAN OUR ENDPOINTS PLEASE CHECK [README](https://github.com/hubofvalley/Testnet-Guides/blob/main/0g%20\(zero-gravity\)/README.md)

```bash
read -p "Enter json-rpc: " BLOCKCHAIN_RPC_ENDPOINT && echo "Current json-rpc: $BLOCKCHAIN_RPC_ENDPOINT"
```

```bash
ENR_ADDRESS=$(wget -qO- eth0.me)
echo "export ENR_ADDRESS=${ENR_ADDRESS}" >> ~/.bash_profile
echo 'export ZGS_LOG_DIR="$HOME/0g-storage-node/run/log"' >> ~/.bash_profile
echo 'export ZGS_LOG_SYNC_BLOCK="802"' >> ~/.bash_profile
echo 'export LOG_CONTRACT_ADDRESS="0x8873cc79c5b3b5666535C825205C9a128B1D75F1"' >> ~/.bash_profile
echo 'export MINE_CONTRACT="0x85F6722319538A805ED5733c5F4882d96F1C7384"' >> ~/.bash_profile
echo "export BLOCKCHAIN_RPC_ENDPOINT=\"$BLOCKCHAIN_RPC_ENDPOINT\"" >> ~/.bash_profile

source ~/.bash_profile

echo -e "\n\033[31mCHECK YOUR STORAGE NODE VARIABLES\033[0m\n\nLOG_CONTRACT_ADDRESS: $LOG_CONTRACT_ADDRESS\nMINE_CONTRACT: $MINE_CONTRACT\nZGS_LOG_SYNC_BLOCK: $ZGS_LOG_SYNC_BLOCK\nBLOCKCHAIN_RPC_ENDPOINT: $BLOCKCHAIN_RPC_ENDPOINT\n\n"
```

ALSO CHECK THE JSON-RPC SYNC, MAKE SURE IT'S IN THE LATEST BLOCK

```bash
curl -s -X POST $BLOCKCHAIN_RPC_ENDPOINT -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' | jq -r '.result' | xargs printf "%d\n"
```

```bash
# Extract Ethereum private key from the specified wallet
0gchaind keys unsafe-export-eth-key $WALLET_NAME
read -sp "Enter your extracted private key: " PRIVATE_KEY && echo

# Update the miner_key in the config.toml file
sed -i 's|^miner_key = ""|miner_key = "'"$PRIVATE_KEY"'"|' $HOME/0g-storage-node/run/config.toml
```

Set parameters in config.toml

```bash
sed -i '
s|^miner_key = ""|miner_key = "'"$PRIVATE_KEY"'"|
s|^\s*#\?\s*network_dir\s*=.*|network_dir = "network"|
s|^\s*#\?\s*network_enr_address\s*=.*|network_enr_address = "'"$ENR_ADDRESS"'"|
s|^\s*#\?\s*network_enr_tcp_port\s*=.*|network_enr_tcp_port = 1234|
s|^\s*#\?\s*network_enr_udp_port\s*=.*|network_enr_udp_port = 1234|
s|^\s*#\s*watch_loop_wait_time_ms\s*=.*|watch_loop_wait_time_ms = 1000|
s|^\s*#\?\s*network_libp2p_port\s*=.*|network_libp2p_port = 1234|
s|^\s*#\?\s*network_discovery_port\s*=.*|network_discovery_port = 1234|
s|^\s*#\s*rpc_listen_address\s*=.*|rpc_listen_address = "0.0.0.0:5678"|
s|^\s*#\s*rpc_listen_address_admin\s*=.*|rpc_listen_address_admin = "0.0.0.0:5679"|
s|^\s*#\?\s*rpc_enabled\s*=.*|rpc_enabled = true|
s|^\s*#\?\s*db_dir\s*=.*|db_dir = "db"|
s|^\s*#\?\s*log_config_file\s*=.*|log_config_file = "log_config"|
s|^\s*#\?\s*log_directory\s*=.*|log_directory = "log"|
s|^\s*#\?\s*network_boot_nodes\s*=.*|network_boot_nodes = \["/ip4/54.219.26.22/udp/1234/p2p/16Uiu2HAmTVDGNhkHD98zDnJxQWu3i1FL1aFYeh9wiQTNu4pDCgps","/ip4/52.52.127.117/udp/1234/p2p/16Uiu2HAkzRjxK2gorngB1Xq84qDrT4hSVznYDHj6BkbaE4SGx9oS","/ip4/18.167.69.68/udp/1234/p2p/16Uiu2HAm2k6ua2mGgvZ8rTMV8GhpW71aVzkQWy7D37TTDuLCpgmX"]|
s|^\s*#\?\s*log_contract_address\s*=.*|log_contract_address = "'"$LOG_CONTRACT_ADDRESS"'"|
s|^\s*#\?\s*mine_contract_address\s*=.*|mine_contract_address = "'"$MINE_CONTRACT"'"|
s|^\s*#\?\s*log_sync_start_block_number\s*=.*|log_sync_start_block_number = '"$ZGS_LOG_SYNC_BLOCK"'|
s|^\s*#\?\s*blockchain_rpc_endpoint\s*=.*|blockchain_rpc_endpoint = "'"$BLOCKCHAIN_RPC_ENDPOINT"'"|
' $HOME/0g-storage-node/run/config.toml
```

### **Create zgs service (storage node) for your node to run in the background** <a href="#create-zgs-service-storage-node-for-your-node-to-run-in-the-background" id="create-zgs-service-storage-node-for-your-node-to-run-in-the-background"></a>

```bash
sudo tee /etc/systemd/system/zgs.service > /dev/null <<EOF
[Unit]
Description=ZGS Node
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/0g-storage-node/run
ExecStart=$HOME/0g-storage-node/target/release/zgs_node --config $HOME/0g-storage-node/run/config.toml
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

**Start Storage node**

```bash
sudo systemctl daemon-reload && \
sudo systemctl enable zgs && \
sudo systemctl restart zgs
```

**Check version**

$HOME/0g-storage-node/target/release/zgs\_node --version

**Show logs by date**

* full logs command

```bash
tail -f ~/0g-storage-node/run/log/zgs.log.$(TZ=UTC date +%Y-%m-%d)
```

* tx\_seq-only logs command

```bash
tail -f ~/0g-storage-node/run/log/zgs.log.$(TZ=UTC date +%Y-%m-%d) | grep tx_seq:
```

* minimized-logs command

```bash
tail -f ~/0g-storage-node/run/log/zgs.log.$(TZ=UTC date +%Y-%m-%d) | grep -v "discv5\|network\|connect\|16U\|nounce"
```

* check your storage node through rpc

```bash
curl -X POST http://localhost:5678 -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"zgs_getStatus","params":[],"id":1}'  | jq
```

### Test Stoarge Node with storage CLI <a href="#test-stoarge-node-with-storage-cli" id="test-stoarge-node-with-storage-cli"></a>

Build Storage CLI with source code

```bash
git clone https://github.com/0glabs/0g-storage-client.git
cd 0g-storage-client
go build
```

Generate Test file for uploading via Storage CLI

```bash
cd $HOME/0g-storage-client
./0g-storage-client gen --file test.txt
```

Upload test file with storage CLI

```bash
./0g-storage-client upload \
--url $BLOCKCHAIN_RPC_ENDPOINT \
--contract $LOG_CONTRACT_ADDRESS \
--key $PRIVATE_KEY \
--node $STORAGE_RPC_ENDPOINT \
--file test.txt
```

If you see this result, the test was successful

<figure><img src="https://services.dongqn.com/~gitbook/image?url=https%3A%2F%2F4035067140-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FC9MoDmPzf2bJhCnlF4JC%252Fuploads%252F40sxPXO7brPdjtljH8kI%252Fimage.png%3Falt%3Dmedia%26token%3D7e5093a9-be46-4521-b1ab-9775f430c4ca&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=b8221322&#x26;sv=1" alt=""><figcaption></figcaption></figure>
