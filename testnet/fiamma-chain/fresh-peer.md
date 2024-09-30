# 🥦 Fresh Peer

```bash
URL="https://fiamma-rpc.node9x.com/net_info"
```

```bash
response=$(curl -s $URL)
```

```bash
PEERS=$(echo $response | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):" + (.node_info.listen_addr | capture("(?<ip>.+):(?<port>[0-9]+)$").port)' | paste -sd "," -)
```

```bash
echo "PEERS=\"$PEERS\""
```

```
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.symphonyd/config/config.toml
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable fiammad
sudo systemctl restart fiammad && sudo journalctl -u fiammad -f
```

#### ➡️ **Number of peers to which the node connects** <a href="#number-of-peers-to-which-the-node-connects" id="number-of-peers-to-which-the-node-connects"></a>

```bash
curl -Ss localhost:(whatever you typed in custom_port)657/net_info | jq .result.n_peers
```

#### ➡️ **You can see the details of your peers with this code** <a href="#you-can-see-the-details-of-your-peers-with-this-code" id="you-can-see-the-details-of-your-peers-with-this-code"></a>

```bash
curl -sS http://localhost:(whatever you typed in custom_port)657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

## Latest Snapshot

(https://raw.githubusercontent.com/0xChicharito/Fiamma-block/main/log-summary.md?token=GHSAT0AAAAAACWCEJ6AFJEZUZF46OWOUVIYZWAEHUQ)
