# ⛓️ Upgrade (v0.12.1)

**1. Install go**

```bash
cd $HOME && \ver="1.22.0" && \wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \sudo rm -rf /usr/local/go && \sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \rm "go$ver.linux-amd64.tar.gz" && \[ ! -f ~/.bash_profile ] && touch ~/.bash_profile && \echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> ~/.bash_profile && \source ~/.bash_profile && \go version
```

**2. Install Cosmovisor**

```bash
source $HOME/.bash_profile
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```

* Check cosmovisor version:

```bash
cosmovisor version
```

**3. Init Cosmovisor**

* Set DAEMON\_HOME:

```bash
export DAEMON_NAME=story
echo "export DAEMON_NAME=story" >> $HOME/.bash_profile
export DAEMON_HOME=$HOME/.story/story
echo "export DAEMON_HOME=$HOME/.story/story" >> $HOME/.bash_profile
```

* Initialize Cosmovisor with DAEMON\_HOME:

```bash
cosmovisor init $(whereis -b story | awk '{print $2}')
```

* Create the backup directory: Now that DAEMON\_HOME is defined, you can create the necessary directories.

```bash
mkdir -p $DAEMON_HOME/cosmovisor/backupecho "
export DAEMON_DATA_BACKUP_DIR=$DAEMON_HOME/cosmovisor/backup" >> $HOME/.bash_profile
echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=false" >> $HOME/.bash_profile
```

After setting up, reload your environment variables with:

```bash
source $HOME/.bash_profile
```

**4. Create upgrades folder**

```bash
mkdir -p $HOME/.story/story/cosmovisor/genesis/bin
mkdir -p $HOME/.story/story/cosmovisor/upgrades/v0.12.1/bin
```

* Stop node

```bash
sudo systemctl stop story
```

* Download Story binary v0.12.1

```bash
#download binay
cd $HOME
rm story-linux-amd64
wget https://github.com/piplabs/story/releases/download/v0.12.1/story-linux-amd64
chmod +x story-linux-amd64
#replace binary
sudo cp $HOME/story-linux-amd64 $(which story)
source $HOME/.bash_profile
story version
```

* Copy new binary to upgrades folder

```bash
# Copy new version binary to upgrade folder
sudo cp $HOME/story-linux-amd64 $HOME/.story/story/cosmovisor/upgrades/v0.12.1/bin/story
```

* Add Upgrade Information for new version

```bash
echo '{"name":"v0.12.1","time":"0001-01-01T00:00:00Z","height":322000}' > $HOME/.story/story/cosmovisor/upgrades/v0.12.1/upgrade-info.json
```

**5. Verify the Setup**

```bash
# Check the story version in genesis folder. It should be old version is v0.12.0$HOME/.story/story/cosmovisor/genesis/bin/story version
```

```bash
# Check the new binary version in upgrade folder. It should be new version v0.12.1$HOME/.story/story/cosmovisor/upgrades/v0.12.1/bin/story version
```

```bash
# Check upgrade info
cat $HOME/.story/story/cosmovisor/upgrades/v0.12.1/upgrade-info.json
```

* Update service file:

```bash
sudo tee /etc/systemd/system/story.service > /dev/null <<EOF
[Unit]
Description=Story Consensus Client
After=network.target
[Service]
User=root
Environment="DAEMON_NAME=story"
Environment="DAEMON_HOME=/root/.story/story"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_DATA_BACKUP_DIR=/root/.story/story/data"
Environment="UNSAFE_SKIP_BACKUP=true"
ExecStart=/root/go/bin/cosmovisor run run
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

```bash
sudo systemctl daemon-reload
sudo systemctl start story
sudo systemctl restart story-geth && sleep 5 && sudo systemctl restart story
```

```bash
sudo journalctl -u story -f -o cat
```

**5. Set schedule for upgrade:**

{% hint style="info" %}
Note

To schedule an upgrade to a new client version at a specific block height, cosmovisor should already be running. Once confirmed, open a separate terminal and run:
{% endhint %}

```bash
source $HOME/.bash_profile
cosmovisor add-upgrade v0.12.1 $HOME/.story/story/cosmovisor/upgrades/v0.12.1/bin/story --force --upgrade-height 322000
```

