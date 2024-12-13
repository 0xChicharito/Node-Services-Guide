# ⛓️ Upgrade

```bash
sudo systemctl stop pellcored
cd $HOME
wget -O pellcored https://github.com/0xPellNetwork/network-config/releases/download/v1.0.20-ignite/pellcored-v1.0.20-linux-amd64
chmod +x $HOME/pellcored
mv $HOME/pellcored $HOME/go/bin/
sudo systemctl restart pellcored && sudo journalctl -u pellcored -f

```
