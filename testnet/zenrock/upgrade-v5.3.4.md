# ⛓️ Upgrade (v5.10.4)

### Manual upgrade <a href="#manual" id="manual"></a>

{% hint style="info" %}
Upgrade height: 16600. Please don\`t upgrade before the specified height
{% endhint %}

```bash
cd $HOME
wget -O zenrockd.zip https://github.com/Zenrock-Foundation/zrchain/releases/download/v5.10.4/zenrockd.zip
unzip zenrockd.zip
rm zenrockd.zip
chmod +x $HOME/zenrockd
sudo mv $HOME/zenrockd $(which zenrockd)
sudo systemctl restart zenrockd && sudo journalctl -u zenrockd -f
```

### For Cosmovisor upgrade <a href="#for-cosmovisor-upgrade" id="for-cosmovisor-upgrade"></a>

**Create upgrades folder**

```bash
mkdir -p $HOME/.zrchain/cosmovisor/upgrades/v5.10.4/bin
```

* Download Story binary v5.10.4

```bash
#download binay
ccd $HOME
wget https://github.com/Zenrock-Foundation/zrchain/releases/download/v5.10.4/zenrockd.zip
unzip zenrockd.zip
rm zenrockd.zip
chmod +x $HOME/zenrockd
```

* Copy new binary to upgrades folder

```bash
# Copy new version binary to upgrade folder
sudo cp $HOME/zenrockd $HOME/.zrchain/cosmovisor/upgrades/v5.10.4/bin/zenrockd
```

```bash
sudo systemctl stop zenrockd
```

**Verify the Setup**

```bash
# Check current symlink
ls -l /root/.zrchain/cosmovisor/current
```

```bash
# remove current symlink
rm /root/.zrchain/cosmovisor/current
```

```bash
# Update symlink point to v5.10.4
ln -s $HOME/.zrchain/cosmovisor/upgrades/v5.10.4 $HOME/.zrchain/cosmovisor/current
```

**Restart node**

```bash
sudo systemctl daemon-reload
sudo systemctl start zenrockd
sudo journalctl -u zenrockd -f -o cat
```
