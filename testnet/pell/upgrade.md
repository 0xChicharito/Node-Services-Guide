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
nano upgrade_node.sh
```

```bash
#!/bin/bash

# Navigate to the HOME directory and download the latest version of pellcored
cd "$HOME" && wget -O pellcored https://github.com/0xPellNetwork/network-config/releases/download/v1.1.1-ignite/pellcored-v1.1.1-linux-amd64

# Display the latest downloaded version
echo "Latest Version:"
chmod +x pellcored && "$HOME/pellcored" version || echo "Error: Unable to retrieve latest version"

# Display the current version of pellcored
echo "Current Version:"
pellcored version || echo "Error: Unable to retrieve current version"

# Wait until block height reaches 185000
echo "Waiting until block 185000..."
for (( ;; )); do
    # Extract the RPC port from config.toml
    rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.pellcored/config/config.toml" | cut -d ':' -f 3)
    
    # Fetch the current block height
    height=$(curl -s localhost:$rpc_port/status | jq -r .result.sync_info.latest_block_height)
    echo "Current Block Height: $height"
    
    # Check if the height has reached 185000
    if (( height == 185000 )); then
        mv pellcored /root/go/bin/
        sudo systemctl daemon-reload
        echo "Upgrading pellcored to v1.1.1..."
        pellcored version && echo "Your node successfully upgraded to v1.1.1!" || echo "Error: Unable to verify version"
        sudo systemctl restart pellcored
        break
    fi
    
    # Wait 5 seconds before rechecking
    sleep 5
done

# Close any running tmux session (you can delete this command below if you want to keep the tmux)
echo "Closing tmux session if active..." && sleep 10
tmux kill-session || echo "No tmux session found to kill"

```

### Make the script executable:

```bash
chmod +x upgrade_node.sh
```

### Run the script:

```bash
bash upgrade_node.sh
```

