# ⛓️ Upgrade (v0.6.5.2)

```bash
sudo systemctl stop kopid
cd $HOME
wget -O kopid https://github.com/kopi-money/kopi/releases/download/v0.6.5.2/kopid-v0.6.5.2-linux-amd64-static
chmod +x $HOME/kopid
mv $HOME/kopid $HOME/go/bin/
sudo systemctl restart kopid && sudo journalctl -u kopid -f
```
