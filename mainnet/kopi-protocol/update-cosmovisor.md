# 🪢 Update Cosmovisor

**Stop node**

```bash
sudo systemctl stop kopid
```

#### Download install script: <a href="#download-install-script" id="download-install-script"></a>

```bash
cd $HOME
wget https://raw.githubusercontent.com/0xChicharito/Cosmovisor/refs/heads/main/kopi-cosmovisor.sh
```

#### Check script content: <a href="#check-script-content" id="check-script-content"></a>

```bash
nano kopi-cosmovisor.sh
```

```bash
#!/bin/bash

if [ -z "$1" ]; then
    echo "Usage: $0 <Kopi Home>"
    exit 1
fi

go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest

export DAEMON_NAME=kopid
echo "export DAEMON_NAME=kopid" >> $HOME/.bash_profile
export DAEMON_HOME=$1
echo "export DAEMON_HOME=$1" >> $HOME/.bash_profile
cosmovisor init $(whereis -b kopid | awk '{print $2}')
mkdir $DAEMON_HOME/cosmovisor/backup
echo "export DAEMON_DATA_BACKUP_DIR=$DAEMON_HOME/cosmovisor/backup" >> $HOME/.bash_profile
echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=true" >> $HOME/.bash_profile
```

#### Run script: <a href="#run-script" id="run-script"></a>

```bash
source $HOME/.bash_profile
chmod +x kopi-cosmovisor.sh
./init-cosmovisor.sh $HOME/.kopid
source $HOME/.bash_profile
```

```bash
sudo tee /etc/systemd/system/kopid.service > /dev/null << EOF
[Unit]
Description=Cosmovisor Kopi Protocal 
After=network-online.target

[Service]
User=root
Type=simple
ExecStart=/root/go/bin/cosmovisor run run
Restart=on-failure
LimitNOFILE=65535
Environment="DAEMON_NAME=kopid"
Environment="DAEMON_HOME=/root/.kopid"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_DATA_BACKUP_DIR=/root/.kopid/cosmovisor/backup"
Environment="UNSAFE_SKIP_BACKUP=true"

[Install]
WantedBy=multi-user.target
EOF
```

**Reload daemon and restart node**

```bash
sudo systemctl daemon-reload && \
sudo systemctl enable kopid.service && \
sudo systemctl restart kopid & sudo systemctl status kopid && \
sudo journalctl -u kopid -f --no-hostname -o cat
```

#### Download Story binary v0.6.5.1

```bash
cd $HOME
wget -O kopid https://github.com/kopi-money/kopi/releases/download/v0.6.5.1/kopid-v0.6.5.1-linux-amd64
chmod +x kopid
mkdir -p $HOME/.kopid/cosmovisor/upgrades/v0.6.5.1/bin
mv kopid $HOME/.kopid/cosmovisor/upgrades/v0.6.5.1/bin/
```

#### Add Upgrade Information for new version

```bash
echo '{"name":"v0.6.5.1","time":"0001-01-01T00:00:00Z","height":92500}' > $HOME/.kopid/cosmovisor/upgrades/v0.6.5.1/upgrade-info.json
```

#### &#x20;**Verify the Setup**

```bash
# Check the kopi version in current folder. It should be old version is v0.6.5.1
$HOME/.kopid/cosmovisor/upgrades/v0.6.5.1/bin/kopid version
```

**Set schedule for upgrade:**

{% hint style="info" %}
To schedule an upgrade to a new client version at a specific block height, cosmovisor should already be running. Once confirmed, open a separate terminal and run:
{% endhint %}

```bash
source $HOME/.bash_profile
cosmovisor add-upgrade v0.6.5.1 $HOME/.kopid/cosmovisor/upgrades/v0.6.5.1/bin/kopid --force --upgrade-height 92500
```
