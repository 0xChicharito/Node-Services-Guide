# ⛓️ Upgrade



> Do not upgrade before **451850**

### Manual&#x20;

```bash
sudo systemctl stop junctiond
cd $HOME
wget -O junctiond https://github.com/airchains-network/junction/releases/download/v0.3.2/junctiond-linux-amd64
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

# Kiểm tra quyền root
if [[ $EUID -ne 0 ]]; then
    echo "This script must be run as root!" >&2
    exit 1
fi

# Tải phiên bản mới nhất của junctiond
cd "$HOME" && wget -O junctiond https://github.com/airchains-network/junction/releases/download/v0.3.2/junctiond-linux-amd64

# Hiển thị phiên bản vừa tải
echo "Latest Version:"
chmod +x "$HOME/junctiond" && "$HOME/junctiond" version || echo "Error: Unable to retrieve latest version"

# Hiển thị phiên bản hiện tại
echo "Current Version:"
/usr/local/bin/junctiond version || echo "Error: Unable to retrieve current version"

# Đợi đến block height đạt 451850
echo "Waiting until block 451850..."
for (( ;; )); do
    # Lấy cổng RPC từ config.toml
    rpc_port=$(grep -m1 'laddr = ' "$HOME/.junctiond/config/config.toml" | awk -F':' '{print $3}' | tr -d '"')

    # Nếu không tìm được port, dùng mặc định 26657
    if [[ -z "$rpc_port" ]]; then
        rpc_port=26657
    fi

    # Lấy block height hiện tại
    height=$(curl -s localhost:$rpc_port/status | jq -r .result.sync_info.latest_block_height)

    # Kiểm tra nếu height không hợp lệ
    if [[ -z "$height" || "$height" == "null" ]]; then
        echo "[$(date '+%F %T')] Could not fetch block height from RPC at port $rpc_port. Retrying..."
        sleep 5
        continue
    fi

    echo "[$(date '+%F %T')] Current Block Height: $height"

    if (( height == 451850 )); then
        echo "Target block height reached. Upgrading junctiond..."

        # Di chuyển binary mới vào /usr/local/bin
        mv "$HOME/junctiond" /usr/local/bin/

        # Reload systemd và restart node
        sudo systemctl daemon-reload
        echo "Upgrading junctiond to v0.3.2..."
        /usr/local/bin/junctiond version && echo "✅ Node successfully upgraded to v0.3.2!" || echo "❌ Error: Unable to verify version"

        sudo systemctl restart junctiond
        break
    fi

    # Chờ 5 giây trước khi kiểm tra lại
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
