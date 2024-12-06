# ⛓️ Upgrade (v0.13.1)

#### # For manual upgrade <a href="#for-manual-upgrade" id="for-manual-upgrade"></a>

```bash
sudo systemctl stop story
```

**Download new story binary v0.13.1**

```bash
cd $HOME
rm story-linux-amd64
wget https://github.com/piplabs/story/releases/download/v0.13.1/story-linux-amd64
```

**Replace new binary version**

```bash
chmod +x story-linux-amd64cp $HOME/story-linux-amd64 $HOME/go/bin/story
source $HOME/.bash_profilestory version
```

**Restart node**

```bash
sudo systemctl daemon-reload && \
sudo systemctl start story && \
sudo journalctl -u story -f -o cat
```

#### # For Cosmovisor upgrade <a href="#for-cosmovisor-upgrade" id="for-cosmovisor-upgrade"></a>

**Create upgrades folder**

```bash
mkdir -p $HOME/.story/story/cosmovisor/upgrades/v0.13.1/bin
```

* Download Story binary v0.13.1

```bash
#download binay
cd $HOME
rm story-linux-amd64
wget https://github.com/piplabs/story/releases/download/v0.13.1/story-linux-amd64
chmod +x story-linux-amd64
```

* Copy new binary to upgrades folder

```bash
# Copy new version binary to upgrade folder
sudo cp $HOME/story-linux-amd64 $HOME/.story/story/cosmovisor/upgrades/v0.13.1/bin/story
```

```bash
sudo systemctl stop story
```

**Verify the Setup**

```bash
# Check current symlink
ls -l /root/.story/story/cosmovisor/current
```

```bash
# remove current symlink
rm /root/.story/story/cosmovisor/current
```

```bash
# Update symlink point to v0.13.1
ln -s $HOME/.story/story/cosmovisor/upgrades/v0.13.1 $HOME/.story/story/cosmovisor/current
```

**Restart node**

```bash
sudo systemctl daemon-reload && \
sudo systemctl start story && \
sudo journalctl -u story -f -o cat
```
