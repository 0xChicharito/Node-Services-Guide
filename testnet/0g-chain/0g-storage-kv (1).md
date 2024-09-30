---
description: 'Recommended Hardware: 8 Cores, 16GB RAM, 260GB of storage (NVME)'
---

# ☑️ 0G Storage KV

#### Install dependencies for building from source

```
sudo apt-get update
sudo apt-get install clang cmake build-essential
```

#### 2. install go

```
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

#### 3. install rustup

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

#### 4. set vars

PLEASE INPUT YOUR STORAGE NODE URL (http://STORAGE\_NODE\_IP:5678,http://STORAGE\_NODE\_IP:5679) , YOUR JSON RPC ENDPOINT (VALIDATOR\_NODE\_IP:8545) OR YOU CAN OUR ENDPOINTS PLEASE CHECK [README](https://github.com/hubofvalley/Testnet-Guides/blob/main/0g%20\(zero-gravity\)/README.md)

```
read -p "Enter json-rpc: " BLOCKCHAIN_RPC_ENDPOINT && echo "Current json-rpc: $BLOCKCHAIN_RPC_ENDPOINT" &&
read -p "Enter storage node urls: " ZGS_NODE && echo "Current storage node urls: $ZGS_NODE" &&
echo 'export ZGS_LOG_SYNC_BLOCK="802"' >> ~/.bash_profile
echo "export ZGS_NODE=\"$ZGS_NODE\"" >> ~/.bash_profile
echo 'export LOG_CONTRACT_ADDRESS="0x8873cc79c5b3b5666535C825205C9a128B1D75F1"' >> ~/.bash_profile
echo 'export MINE_CONTRACT="0x85F6722319538A805ED5733c5F4882d96F1C7384"' >> ~/.bash_profile
echo "export BLOCKCHAIN_RPC_ENDPOINT=\"$BLOCKCHAIN_RPC_ENDPOINT\"" >> ~/.bash_profile

source ~/.bash_profile

echo -e "\n\033[31mCHECK YOUR STORAGE KV VARIABLES\033[0m\n\nZGS_NODE: $ZGS_NODE\nLOG_CONTRACT_ADDRESS: $LOG_CONTRACT_ADDRESS\nMINE_CONTRACT: $MINE_CONTRACT\nZGS_LOG_SYNC_BLOCK: $ZGS_LOG_SYNC_BLOCK\nBLOCKCHAIN_RPC_ENDPOINT: $BLOCKCHAIN_RPC_ENDPOINT\n\n"
```

#### 5. download binary

```
cd $HOME
git clone https://github.com/0glabs/0g-storage-kv.git
cd $HOME/0g-storage-kv
git fetch
git checkout tags/v1.1.0-testnet
git submodule update --init
sudo apt install cargo
```

then build it

```
cargo build --release
```

#### 6. copy a config\_example.toml file

```
cp /$HOME/0g-storage-kv/run/config_example.toml /$HOME/0g-storage-kv/run/config.toml
```

#### 7. update storage kv configuration

```
sed -i '
s|^\s*#\?\s*rpc_enabled\s*=.*|rpc_enabled = true|
s|^\s*#\?\s*rpc_listen_address\s*=.*|rpc_listen_address = "0.0.0.0:6789"|
s|^\s*#\?\s*db_dir\s*=.*|db_dir = "db"|
s|^\s*#\?\s*kv_db_dir\s*=.*|kv_db_dir = "kv.DB"|
s|^\s*#\?\s*log_config_file\s*=.*|log_config_file = "log_config"|
s|^\s*#\?\s*log_contract_address\s*=.*|log_contract_address = "'"$LOG_CONTRACT_ADDRESS"'"|
s|^\s*#\?\s*zgs_node_urls\s*=.*|zgs_node_urls = "'"$ZGS_NODE"'"|
s|^\s*#\?\s*mine_contract_address\s*=.*|mine_contract_address = "'"$MINE_CONTRACT"'"|
s|^\s*#\?\s*log_sync_start_block_number\s*=.*|log_sync_start_block_number = '"$ZGS_LOG_SYNC_BLOCK"'|
s|^\s*#\?\s*blockchain_rpc_endpoint\s*=.*|blockchain_rpc_endpoint = "'"$BLOCKCHAIN_RPC_ENDPOINT"'"|
' $HOME/0g-storage-kv/run/config.toml
```

#### 8. create service

```
sudo tee /etc/systemd/system/zgskv.service > /dev/null <<EOF
[Unit]
Description=ZGS KV
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/0g-storage-kv/run
ExecStart=$HOME/0g-storage-kv/target/release/zgs_kv --config $HOME/0g-storage-kv/run/config.toml
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

#### 9. start the node

```
sudo systemctl daemon-reload && \
sudo systemctl enable zgskv && \
sudo systemctl start zgskv && \
sudo systemctl status zgskv
```

#### 10. check the logs

```
sudo journalctl -u zgskv -fn 100 -o cat
```

MAKE SURE YOUR LOGS HAS THE SYNCED TX\_SEQ(tx sequence) VALUE, CHECK [STORAGE SCAN](https://storagescan-newton.0g.ai/)&#x20;

<figure><img src="https://private-user-images.githubusercontent.com/100946299/340293130-ce2d8707-190d-4931-8ed1-44c1447fe360.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjEyMzc2MTAsIm5iZiI6MTcyMTIzNzMxMCwicGF0aCI6Ii8xMDA5NDYyOTkvMzQwMjkzMTMwLWNlMmQ4NzA3LTE5MGQtNDkzMS04ZWQxLTQ0YzE0NDdmZTM2MC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNzE3JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDcxN1QxNzI4MzBaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0yZjEyYjZkNTZiOTBiMTM3ZDU1N2RhZmU1Mjc3MjQwYjM2ZTc0ZjE3ZWJmNjViYTcwMzI3M2M0ZGM1NzlkYzQxJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.i5wxCr5rU88-OC6MH9T49WfwQ6IbtHufsch0y4S_9yk" alt=""><figcaption></figcaption></figure>

### delete the node



```
sudo systemctl stop zgskv
sudo systemctl disable zgskv
sudo rm -rf /etc/systemd/system/zgskv.service
sudo rm -rf 0g-storage-kv
```
