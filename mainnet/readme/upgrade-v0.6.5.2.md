# ⛓️ Upgrade (v0.6.5.2)

```bash
sudo systemctl stop kopid
cd $HOME
wget -O kopid https://github.com/kopi-money/kopi/releases/download/v11/kopid-v11-linux-amd64
chmod +x $HOME/kopid
mv $HOME/kopid $HOME/go/bin/
sudo systemctl restart kopid && sudo journalctl -u kopid -f
```

### Auto upgrade&#x20;

```bash
#Create new tmux
tmux new -s kopi_upgrade
```

```bash
nano upgrade_node.sh
```

```bash
#!/bin/bash

# Ki?m tra quy?n root
if [[ $EUID -ne 0 ]]; then
    echo "This script must be run as root!" >&2
    exit 1
fi

# T?i phiên b?n m?i nh?t c?a kopid
cd "$HOME" && wget -O kopid https://github.com/kopi-money/kopi/releases/download/v11/kopid-v11-linux-amd64

# Hi?n th? phiên b?n v?a t?i
echo "Latest Version:"
chmod +x $HOME/kopid && "$HOME/kopid" version || echo "Error: Unable to retrieve latest version"

# Hi?n th? phiên b?n hi?n t?i
echo "Current Version:"
/root/go/bin/kopid version || echo "Error: Unable to retrieve current version"

# Ð?i block height d?t 3943500
echo "Waiting until block 3943500..."
for (( ;; )); do
    # L?y c?ng RPC t? config.toml
    rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.kopid/config/config.toml" | cut -d ':' -f 3)
    
    # L?y block height hi?n t?i
    height=$(curl -s localhost:$rpc_port/status | jq -r .result.sync_info.latest_block_height)
    echo "Current Block Height: $height"
    
    # Ki?m tra n?u height d?t 3943500
    if (( height == 3943500 )); then

        # Di chuy?n kopid vào dúng thu m?c
        mv "$HOME/kopid" /root/go/bin/
        sudo systemctl daemon-reload
        echo "Upgrading kopid to v11..."
        /root/go/bin/kopid version && echo "Your node successfully upgraded to v11!" || echo "Error: Unable to verify version"
        sudo systemctl restart kopid
        break
    fi
    
    # Ch? 5 giây tru?c khi ki?m tra l?i
    sleep 5
done

```

### Make the script executable:

```bash
chmod +x upgrade_node.sh
```

### Run the script:

```bash
sudo ./upgrade_node.sh
```
