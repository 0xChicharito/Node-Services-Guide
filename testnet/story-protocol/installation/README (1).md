---
cover: ../../../.gitbook/assets/1080x360 (1) (1).jpg
coverY: 0
---

# ⚙️ Installation

### System Specs

| Hardware  | Requirement |
| --------- | ----------- |
| CPU       | 4 Cores     |
| RAM       | 8 GB        |
| Disk      | 200 GB      |
| Bandwidth | 10 MBit/s   |

## Download Story-Geth binary <a href="#download-story-geth-binary" id="download-story-geth-binary"></a>

```bash
wget https://story-geth-binaries.s3.us-west-1.amazonaws.com/geth-public/geth-linux-amd64-0.9.2-ea9f0d2.tar.gz
tar -xzvf geth-linux-amd64-0.9.2-ea9f0d2.tar.gz
sudo cp geth-linux-amd64-0.9.2-ea9f0d2/geth $HOME/go/bin/story-geth
source $HOME/.bash_profile
story-geth version
```

### Download Story binary <a href="#download-story-binary" id="download-story-binary"></a>

```bash
wget https://story-geth-binaries.s3.us-west-1.amazonaws.com/story-public/story-linux-amd64-0.9.11-2a25df1.tar.gz
tar -xzvf story-linux-amd64-0.9.11-2a25df1.tar.gz
sudo cp story-linux-amd64-0.9.11-2a25df1/story $HOME/go/bin/story
source $HOME/.bash_profile
story version
```

### Init Iliad node <a href="#init-iliad-node" id="init-iliad-node"></a>

```bash
story init --network iliad
```

### Create story-geth service file <a href="#create-story-geth-service-file" id="create-story-geth-service-file"></a>

```bash
sudo tee /etc/systemd/system/story-geth.service > /dev/null <<EOF
[Unit]
Description=Story Geth Client
After=network.target

[Service]
User=root
ExecStart=/root/go/bin/story-geth --iliad --syncmode full
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

### Create story service file <a href="#create-story-service-file" id="create-story-service-file"></a>

```bash
sudo tee /etc/systemd/system/story.service > /dev/null <<EOF
[Unit]
Description=Story Consensus Client
After=network.target

[Service]
User=root
ExecStart=/root/go/bin/story run
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

### Reload and start story-geth <a href="#reload-and-start-story-geth" id="reload-and-start-story-geth"></a>

```bash
sudo systemctl daemon-reload && \
sudo systemctl start story-geth && \
sudo systemctl enable story-geth && \
sudo systemctl status story-geth
```

### Reload and start story <a href="#reload-and-start-story" id="reload-and-start-story"></a>

```bash
sudo systemctl daemon-reload && \
sudo systemctl start story && \
sudo systemctl enable story && \
sudo systemctl status story
```

### Check logs <a href="#check-logs" id="check-logs"></a>

```bash
sudo journalctl -u story-geth -f -o cat
```

Wait a minute for connect peers

```bash
sudo journalctl -u story -f -o cat
```

### Check sync status <a href="#check-sync-status" id="check-sync-status"></a>

```bash
curl localhost:26657/status | jq
```

_Waiting for your node`catching_up` is `false`you can create validator._

### Create validator <a href="#create-validator" id="create-validator"></a>

Export private key

```bash
story validator export --export-evm-key
```

Your EVM Private Key saved to: `/root/.story/story/config/private_key.txt`

{% hint style="info" %}
_Please keep your private key in a safe place_
{% endhint %}

```bash
story validator create --stake 1000000000000000000 --private-key "your_private_key"
```

_Backup the validator key:_

* _File location: `/root/.story/story/config/priv_validator_key.json`_
* _Copy this file to your local machine._
* _Store it carefully; this is the most crucial key for your validator._

## Check health of node

```bash
curl localhost:26657/health
```

{% hint style="info" %}
This will let you know if the node is healthy -`{}` indicates it is
{% endhint %}

## Delete node

```bash
sudo systemctl stop story story-geth
sudo systemctl disable story story-geth
rm -rf $HOME/.story
sudo rm /etc/systemd/system/story.service /etc/systemd/system/story-geth.service
sudo systemctl daemon-reload
```
