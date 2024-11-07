# Upgrade

All node operators must upgrade to Story client version v0.12.1 at block height 322,000.

**Stop node**

```bash
sudo systemctl stop story
```

**Download new binary v0.12.1**

```bash
cd $HOME
rm story-linux-amd64wget https://github.com/piplabs/story/releases/download/v0.12.1/story-linux-amd64
```

**Replace new binary version**

```bash
chmod +x story-linux-amd64
cp $HOME/story-linux-amd64 $HOME/go/bin/storysource 
$HOME/.bash_profile
story version
```

**Restart node**

```bash
sudo systemctl daemon-reload && \
sudo systemctl start story && \
sudo systemctl status story && \
sudo journalctl -u story -f -o cat
```
