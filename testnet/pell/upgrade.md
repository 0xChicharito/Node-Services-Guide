# ⛓️ Upgrade  (v1.1.1)

### Manual&#x20;

```bash
sudo systemctl stop pellcored
cd $HOME
wget -O pellcored https://github.com/0xPellNetwork/network-config/releases/download/v1.0.20-ignite/pellcored-v1.0.20-linux-amd64
chmod +x $HOME/pellcored
mv $HOME/pellcored $HOME/go/bin/
sudo systemctl restart pellcored && sudo journalctl -u pellcored -f

```

### Auto upgrade&#x20;

```bash
#Create new tmux
tmux new -s pell
```

```bash
#!/bin/bash
cd $HOME && wget -O pellcored https://github.com/0xPellNetwork/network-config/releases/download/v1.1.1-ignite/pellcored-v1.1.1-linux-amd64
echo "Latest Version:"
chmod +x pellcored && $HOME/pellcored version
echo "Current version:"
pellcored version
echo "Wait until block 185000..."
for((;;)); do
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.pellcored/config/config.toml" | cut -d ':' -f 3)
height=$(curl -s localhost:$rpc_port/status | jq -r .result.sync_info.latest_block_height)
echo $height
if ((height==185000)); then
mv pellcored /root/go/bin/
sudo systemctl daemon-reload
echo "Version:"
pellcored version && echo "Your node successfully upgraded to v1.1.1 " && sleep 5 
sudo systemctl restart pellcored
break
fi
sleep 5
done
echo "Wait for tmux window to close" && sleep 10
tmux kill-session
```

### Exit the tmux&#x20;

{% hint style="info" %}
Ctr + B + C
{% endhint %}

