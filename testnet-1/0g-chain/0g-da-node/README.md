---
description: 'Recommended Hardware: 8 Cores, 16GB RAM, 1TB of storage (NVME)'
---

# ☑️ 0G DA Node

### **System updates, installation of required dependencies** <a href="#system-updates-installation-of-required-dependencies" id="system-updates-installation-of-required-dependencies"></a>

```
sudo apt-get update
sudo apt install make clang pkg-config libssl-dev
sudo apt-get install libssl-dev
apt-get install protobuf-compiler
sudo apt install build-essential curl
```

**Install rust**

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
```

Installation

```
cd $HOME
git clone https://github.com/0glabs/0g-da-node.git
cd $HOME/0g-da-node
cargo build --release
./dev_support/download_params.sh
```

Or in zsh

```
bash /root/0g-da-node/dev_support/download_params.sh
```

**Setup your variable settings**

```
sudo cp $HOME/0g-da-node/config_example.toml $HOME/0g-da-node/config.toml
```

On the first run of DA node, it will register the signer information in DA contract. To generate a BLS private key if don't have:

```
cargo run --bin key-gen
```

#### Config.toml set

```
mv /root/0g-da-node/config_example.toml /root/0g-da-node/config.toml
sudo nano /root/0g-da-node/config.toml
```

grpc\_listen\_address = "0.0.0.0:34000" eth\_rpc\_endpoint = "http://Validator rpc ip:8545" socket\_address = "your node ip:34000" da\_entrance\_address = "0xDFC8B84e3C98e8b550c7FEF00BCB2d8742d80a69" start\_block\_number = 802 signer\_bls\_private\_key = "Bls key gen paste" signer\_eth\_private\_key = "validator eth private key"

### **Create** zgda **service for your node to run in the background** <a href="#create-zgda-service-for-your-node-to-run-in-the-background" id="create-zgda-service-for-your-node-to-run-in-the-background"></a>

```
sudo tee /etc/systemd/system/0gda.service > /dev/null <<EOF
[Unit]
Description=0G-DA Node
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/0g-da-node
ExecStart=$HOME/0g-da-node/target/release/server --config $HOME/0g-da-node/config.toml
Restart=always
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

```

**Start DA node**

```
sudo systemctl daemon-reload && \
sudo systemctl enable 0gda && \
sudo systemctl start 0gda && \
sudo systemctl status 0gda
```

**Check log**

```
sudo journalctl -u 0gda -f -o cat
```
