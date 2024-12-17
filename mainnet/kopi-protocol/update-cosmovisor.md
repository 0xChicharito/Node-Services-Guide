# 🪢 Cosmovisor

```bash
cd $HOME && \
ver="1.22.0" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile && \
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> ~/.bash_profile && \
source ~/.bash_profile && \
go version
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
./kopi-cosmovisor.sh $HOME/.kopid
source $HOME/.bash_profile
```

#### **Create upgrades folder** <a href="#create-upgrades-folder" id="create-upgrades-folder"></a>

```bash
mkdir -p $HOME/.kopid/cosmovisor/genesis/bin
mkdir -p $HOME/.kopid/cosmovisor/upgrades/v0_6_5_2/bin
```

* **Stop node**

```bash
sudo systemctl stop kopid
```

#### **Download binary** v0.6.5.2 <a href="#download-binary-v5.5.0" id="download-binary-v5.5.0"></a>

```bash
cd $HOME
wget -O kopid https://github.com/kopi-money/kopi/releases/download/v0.6.5.2/kopid-v0.6.5.2-linux-amd64-static
chmod +x $HOME/kopid
sudo cp $HOME/kopid $HOME/.kopid/cosmovisor/upgrades/v0_6_5_2/bin/kopid
```

* Add Upgrade Information for new version

```bash
echo '{"name":"v0_6_5_2","time":"0001-01-01T00:00:00Z","height":1350000}' > $HOME/.kopid/cosmovisor/upgrades/v0.6.5.2/upgrade-info.json
```

#### **Verify the Setup** <a href="#verify-the-setup" id="verify-the-setup"></a>

```bash
# Check current symlink
ls -l /root/.kopid/cosmovisor/current
```

```bash
# Check the zenrock version in genesis folder.
$HOME/.kopid/cosmovisor/genesis/bin/kopid version
```

```bash
# Check the new binary version in upgrade folder. It should be new version v0.13.0
$HOME/.kopid/cosmovisor/upgrades/v0_6_5_2/bin/kopid version
```

#### Check upgrade info <a href="#check-upgrade-info" id="check-upgrade-info"></a>

```
cat $HOME/.kopid/cosmovisor/upgrades/v0_6_5_2/upgrade-info.json
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
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=true"
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

**Set schedule for upgrade:**

{% hint style="info" %}
To schedule an upgrade to a new client version at a specific block height, cosmovisor should already be running. Once confirmed, open a separate terminal and run:
{% endhint %}

```bash
source $HOME/.bash_profile
cosmovisor add-upgrade v0_6_5_2 $HOME/.kopid/cosmovisor/upgrades/v0_6_5_2/bin/kopid --force --upgrade-height 1350000
```
