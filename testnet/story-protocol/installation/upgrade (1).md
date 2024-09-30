# ⛓️ Upgrade

## Version: <a href="#version" id="version"></a>

```
Version v0.10.0

Do not upgrade before block 626,575
```

## Download new binary <a href="#download-new-binary" id="download-new-binary"></a>

```
cd $HOME
wget https://story-geth-binaries.s3.us-west-1.amazonaws.com/story-public/story-linux-amd64-0.10.0-9603826.tar.gz
tar -xzvf story-linux-amd64-0.10.0-9603826.tar.gz
```

## Stop node <a href="#stop-node" id="stop-node"></a>

```
sudo systemctl stop story
```

## Copy binary to $HOME/go/bin to use it anywhere <a href="#copy-binary-to-usdhome-go-bin-to-use-it-anywhere" id="copy-binary-to-usdhome-go-bin-to-use-it-anywhere"></a>

```
cp $HOME/story-linux-amd64-0.10.0-9603826/story $HOME/go/bin
source $HOME/.bash_profile
story version
```

## Restart node <a href="#restart-node" id="restart-node"></a>

```
sudo systemctl daemon-reload && \
sudo systemctl start story && \
sudo systemctl status story
```

