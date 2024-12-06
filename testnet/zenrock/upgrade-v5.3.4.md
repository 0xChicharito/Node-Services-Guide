# ⛓️ Upgrade (v5.5.0)

### Manual upgrade <a href="#manual" id="manual"></a>

{% hint style="info" %}
Upgrade height: [1259000](https://testnet.itrocket.net/zenrock/block/1259000). Please don\`t upgrade before the specified height
{% endhint %}

```bash
cd $HOME
wget https://github.com/Zenrock-Foundation/zrchain/releases/download/v5.5.0/zenrockd.zip
unzip zenrockd.zip
rm zenrockd.zip
chmod +x $HOME/zenrockd
sudo mv $HOME/zenrockd $(which zenrockd)
sudo systemctl restart zenrockd && sudo journalctl -u zenrockd -f
```
