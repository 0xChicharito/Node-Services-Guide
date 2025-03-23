# 🪢 Cosmovisor

### Install Cosmovisor <a href="#install-cosmovisor" id="install-cosmovisor"></a>

* Check go version #requirement go version above v.1.22 if you have Go v.1.22 already. Please skip install Go step.

```bash
cd $HOME && \
ver="1.22.3" && \
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
wget https://raw.githubusercontent.com/0xChicharito/Cosmovisor/refs/heads/main/init-cosmovisor.sh
```

### Check script content:

```bash
nano init-cosmovisor.sh
```

```bash
#!/bin/bash

if [ -z "$1" ]; then
    echo "Usage: $0 <Junction Home>"
    exit 1
fi

go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest

export DAEMON_NAME=junctiond
echo "export DAEMON_NAME=junctiond" >> $HOME/.bash_profile
export DAEMON_HOME=$1
echo "export DAEMON_HOME=$1" >> $HOME/.bash_profile
cosmovisor init $(whereis -b junctiond | awk '{print $2}')
mkdir $DAEMON_HOME/cosmovisor/backup
echo "export DAEMON_DATA_BACKUP_DIR=$DAEMON_HOME/cosmovisor/backup" >> $HOME/.bash_profile
echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=true" >> $HOME/.bash_profile
```

### Run script:

```bash
source $HOME/.bash_profile
chmod +x init-cosmovisor.sh
./init-cosmovisor.sh $HOME/.junction
source $HOME/.bash_profile
```

### **Download binary v0.2.0**

```bash
cd $HOME
wget -O junctiond https://github.com/airchains-network/junction/releases/download/v0.2.0/junctiond-linux-amd64
chmod +x junctiond
mkdir -p $HOME/.junctiond/cosmovisor/upgrades/jip-2/bin
mv junctiond $HOME/.junctiond/cosmovisor/upgrades/jip-2/bin/
```

### **Verify the Setup**

```bash
# Check the new binary version
$HOME/.junctiond/cosmovisor/upgrades/jip-2/bin/junctiond version
```

### **Update service file:**

```bash
sudo tee /etc/systemd/system/junctiond.service > /dev/null <<EOF
[Unit]
Description=Cosmovisor Junctiond Node
After=network.target

[Service]
User=root
Type=simple
ExecStart=/root/go/bin/cosmovisor run start
Restart=on-failure
LimitNOFILE=65535
Environment="DAEMON_NAME=junctiond"
Environment="DAEMON_HOME=/root/.junction"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=true"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_DATA_BACKUP_DIR=/root/.junctiond/cosmovisor/backup"
Environment="UNSAFE_SKIP_BACKUP=true"

[Install]
WantedBy=multi-user.target
EOF
```

```bash
sudo systemctl daemon-reload
sudo systemctl restart junctiond && sudo journalctl -u junctiond -f
```

{% hint style="info" %}
Hint

To schedule an upgrade to a new client version at a specific block height, cosmovisor should already be running. Once confirmed, open a separate terminal and run:
{% endhint %}

```bash
cosmovisor add-upgrade v0.2.0 /root/.junction/cosmovisor/upgrades/jip-2/bin/junctiond --force --upgrade-height 2383911
```

<figure><img src="../../../.gitbook/assets/Screenshot 2024-10-20 232333.png" alt=""><figcaption></figcaption></figure>
