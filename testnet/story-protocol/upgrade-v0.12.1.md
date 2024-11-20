# 🪢 Upgrade (v0.13.0)

**Upgrade story-geth:**

**Stop story-geth**

```bash
sudo systemctl stop story-geth
```

**Download new story-geth binary v0.10.1**

```bash
cd $HOME
rm geth-linux-amd64
wget https://github.com/piplabs/story-geth/releases/download/v0.10.1/geth-linux-amd64
chmod +x geth-linux-amd64
mv $HOME/geth-linux-amd64 $HOME/go/bin/story-gethsource $HOME/.bash_profilestory-geth version
```

**Start story-geth**

```
sudo systemctl daemon-reload && \
sudo systemctl start story-geth && \
sudo journalctl -u story-geth -f -o cat
```

> **All node operators must upgrade to Story client version v0.130 at block height **<mark style="color:red;">**`858,000`**</mark>

**Stop node**

```bash
sudo systemctl stop story
```

**Download new binary v0.13.0**

```bash
cd $HOME
rm story-linux-amd64
wget https://github.com/piplabs/story/releases/download/v0.13.0/story-linux-amd64
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
