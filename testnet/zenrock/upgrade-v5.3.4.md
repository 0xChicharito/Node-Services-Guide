# ⛓️ Upgrade (v5.3.4)

### Manual upgrade <a href="#manual" id="manual"></a>

```bash
cd $HOME
wget https://github.com/Zenrock-Foundation/zrchain/releases/download/v5.4.0/zenrockd.zip
unzip zenrockd.zip
rm zenrockd.zip
chmod +x $HOME/zenrockd
sudo mv $HOME/zenrockd $(which zenrockd)
sudo systemctl restart zenrockd && sudo journalctl -u zenrockd -f
```
