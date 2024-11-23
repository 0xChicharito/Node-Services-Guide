# 🪢 Upgrade (v0.13.0)

### 1. For manual upgrade

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

> **All node operators must upgrade to Story client version v0.130 at block height&#x20;**<mark style="color:red;">**`858,000`**</mark>

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

### 2. For Cosmovisor auto upgrade <a href="#id-3-for-cosmovisor-auto-upgrade" id="id-3-for-cosmovisor-auto-upgrade"></a>

{% hint style="info" %}
#### Upgrade latest cosmovisor version `v1.7.0`
{% endhint %}

```bash
source $HOME/.bash_profile 
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest 
cosmovisor version
```

**Create upgrades folder**

```bash
mkdir -p $HOME/.story/story/cosmovisor/upgrades/v0.13.0/bin
```

* Download Story binary v0.13.0

```bash
#download binay
cd $HOME
rm story-linux-amd64
wget https://github.com/piplabs/story/releases/download/v0.13.0/story-linux-amd64
chmod +x story-linux-amd64
```

* Copy new binary to upgrades folder

```bash
# Copy new version binary to upgrade folder
sudo cp $HOME/story-linux-amd64 $HOME/.story/story/cosmovisor/upgrades/v0.13.0/bin/story
```

* Add Upgrade Information for new version

```bash
echo '{"name":"v0.13.0","time":"0001-01-01T00:00:00Z","height":858000}' > $HOME/.story/story/cosmovisor/upgrades/v0.13.0/upgrade-info.json
```

**5. Verify the Setup**

```bash
# Check the story version in current folder. It should be old version is v0.12.1
$HOME/.story/story/cosmovisor/upgrades/v0.12.1/bin/story version
```

```bash
# Check the new binary version in upgrade folder. It should be new version v0.13.0
$HOME/.story/story/cosmovisor/upgrades/v0.13.0/bin/story version
```

```bash
# Check upgrade info
cat $HOME/.story/story/cosmovisor/upgrades/v0.13.0/upgrade-info.json
```

**5. Set schedule for upgrade:**

{% hint style="info" %}
Note

To schedule an upgrade to a new client version at a specific block height, cosmovisor should already be running. Once confirmed, open a separate terminal and run:
{% endhint %}

```bash
source $HOME/.bash_profile
cosmovisor add-upgrade v0.13.0 $HOME/.story/story/cosmovisor/upgrades/v0.13.0/bin/story --force --upgrade-height 858000
```

> <mark style="color:red;">`Note`</mark>
>
> From now please do not stop and restart node before block **858,000** . Because it will run forcely with new binary.
>
> If you need to restart the node unexpectedly, please setup again:
>
> * Remove folder
>
> ```
> rm -rf $HOME/.story/story/cosmovisor/upgrades/v0.13.0
> ```
>
> * Remove symlink:
>
> ```
> rm $HOME/.story/story/cosmovisor/current
> ```
>
> * Setup symlink v0.12.1 back:
>
> ```
> ln -s $HOME/.story/story/cosmovisor/upgrades/v0.12.1 $HOME/.story/story/cosmovisor/current
> ```
>
> * Finally Start node & setup v0.13.0 with Cosmosvisor again.
