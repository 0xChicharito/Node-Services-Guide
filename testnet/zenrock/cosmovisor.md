# 🕹️ Cosmovisor

### Install Cosmovisor <a href="#install-cosmovisor" id="install-cosmovisor"></a>

* Check go version #requirement go version above v.1.22 if you have Go v.1.22 already. Please skip install Go step.

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

### Download install script:

```bash
cd $HOME
wget https://raw.githubusercontent.com/0xChicharito/Cosmovisor/refs/heads/main/zenrock-cosmovisor.sh
```

### Check script content:

```bash
nano zenrock-cosmovisor.sh
```

```bash
#!/bin/bash

if [ -z "$1" ]; then
    echo "Usage: $0 <Zenrock Home>"
    exit 1
fi

go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest

export DAEMON_NAME=zenrockd
echo "export DAEMON_NAME=zenrockd" >> $HOME/.bash_profile
export DAEMON_HOME=$1
echo "export DAEMON_HOME=$1" >> $HOME/.bash_profile
cosmovisor init $(whereis -b zenrockd | awk '{print $2}')
mkdir $DAEMON_HOME/cosmovisor/backup
echo "export DAEMON_DATA_BACKUP_DIR=$DAEMON_HOME/cosmovisor/backup" >> $HOME/.bash_profile
echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=true" >> $HOME/.bash_profile
```

### Run script:

```bash
source $HOME/.bash_profile
chmod +x zenrock-cosmovisor.sh
./zenrock-cosmovisor.sh $HOME/.zrchain
source $HOME/.bash_profile
```

### **Create upgrades folder**

```bash
mkdir -p $HOME/.zrchain/cosmovisor/genesis/bin
mkdir -p $HOME/.zrchain/cosmovisor/upgrades/v5.5.0/bin
```

* Stop node

```bash
sudo systemctl stop zenrockd
```

### **Download binary v5.5.0**

<pre class="language-bash"><code class="lang-bash"><strong>cd $HOME
</strong>wget https://github.com/Zenrock-Foundation/zrchain/releases/download/v5.4.0/zenrockd.zip
unzip zenrockd.zip
rm zenrockd.zip
chmod +x $HOME/zenrockd
sudo cp $HOME/zenrockd $HOME/.zrchain/cosmovisor/upgrades/v5.5.0/bin/zenrockd
</code></pre>

* Add Upgrade Information for new version

```bash
echo '{"name":"v5beta2","time":"0001-01-01T00:00:00Z","height":1259000}' > $HOME/.zrchain/cosmovisor/upgrades/v5.5.0/upgrade-info.json
```

### **Verify the Setup**

```bash
# Check current symlink
ls -l /root/.zrchain/cosmovisor/current
```

```bash
# Check the zenrock version in genesis folder.
$HOME/.zrchain/cosmovisor/genesis/bin/zenrockd version
```

```bash
# Check the new binary version in upgrade folder. It should be new version v0.13.0
$HOME/.zrchain/cosmovisor/upgrades/v5.5.0/bin/zenrockd version
```

### Check upgrade info

```bash
cat $HOME/.zrchain/cosmovisor/upgrades/v5.5.0/upgrade-info.json
```

### **Update service file:**

```bash
sudo tee /etc/systemd/system/zenrockd.service > /dev/null <<EOF
[Unit]
Description=Zenrock Consensus Client
After=network.target
[Service]
User=root
Environment="DAEMON_NAME=zenrockd"
Environment="DAEMON_HOME=/root/.zrchain"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=true"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_DATA_BACKUP_DIR=/root/.zrchain/cosmovisor/backup"
Environment="UNSAFE_SKIP_BACKUP=true"
ExecStart=/root/go/bin/cosmovisor run start
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

```bash
sudo systemctl daemon-reload
sudo systemctl start zenrockd
sudo systemctl status zenrockd && sudo journalctl -u zenrockd -f -o cat
```

**Set schedule for upgrade:**

{% hint style="info" %}
To schedule an upgrade to a new client version at a specific block height, cosmovisor should already be running. Once confirmed, open a separate terminal and run:
{% endhint %}

```bash
source $HOME/.bash_profile
cosmovisor add-upgrade v5.5.0 $HOME/.zrchain/cosmovisor/upgrades/v5.5.0/bin/zenrockd --force --upgrade-height 1259000
```

