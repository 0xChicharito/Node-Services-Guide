# ⛓️ Upgrade

### Manual&#x20;

```bash
sudo systemctl stop junctiond
cd $HOME
wget -O junctiond https://github.com/airchains-network/junction/releases/download/v0.3.1/junctiond-linux-amd64
chmod +x $HOME/junctiond
mv $HOME/junctiond $HOME/go/bin/
sudo systemctl restart junctiond && sudo journalctl -u junctiond -f

```

### Auto upgrade&#x20;

```bash
#Create new tmux
tmux new -s junctiond
```

```bash
nano upgrade_airchains.sh
```

```bash
#!/bin/bash

# Ki?m tra quy?n root
if [[ $EUID -ne 0 ]]; then
    echo "This script must be run as root!" >&2
    exit 1
fi

# T?i phiên b?n m?i nh?t c?a kopid
cd "$HOME" && wget -O junctiond https://github.com/airchains-network/junction/releases/download/v0.3.1/junctiond-linux-amd64

# Hi?n th? phiên b?n v?a t?i
echo "Latest Version:"
chmod +x $HOME/junctiond && "$HOME/junctiond" version || echo "Error: Unable to retrieve latest version"

# Hi?n th? phiên b?n hi?n t?i
echo "Current Version:"
/root/go/bin/junctiond version || echo "Error: Unable to retrieve current version"

# Ð?i block height d?t 3943500
echo "Waiting until block 3943500..."
for (( ;; )); do
    # L?y c?ng RPC t? config.toml
    rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.junctiond/config/config.toml" | cut -d ':' -f 3)
    
    # L?y block height hi?n t?i
    height=$(curl -s localhost:$rpc_port/status | jq -r .result.sync_info.latest_block_height)
    echo "Current Block Height: $height"
    
    # Ki?m tra n?u height d?t 3943500
    if (( height == 3943500 )); then

        # Di chuy?n kopid vào dúng thu m?c
        mv "$HOME/junctiond" /root/go/bin/
        sudo systemctl daemon-reload
        echo "Upgrading junctiond to v11..."
        /root/go/bin/junctiond version && echo "Your node successfully upgraded to v11!" || echo "Error: Unable to verify version"
        sudo systemctl restart junctiond
        break
    fi
    
    # Ch? 5 giây tru?c khi ki?m tra l?i
    sleep 5
done

```

### Make the script executable:

```bash
chmod +x upgrade_airchains.sh
```

### Run the script:

```bash
sudo ./upgrade_airchains.sh
```
