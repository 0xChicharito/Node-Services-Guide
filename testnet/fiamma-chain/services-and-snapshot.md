# 🥦 Services & Snapshot

<table data-header-hidden><thead><tr><th width="129"></th><th></th></tr></thead><tbody><tr><td>RPC</td><td><a href="https://fiamma-rpc.node9x.com/">https://fiamma-rpc.node9x.com</a></td></tr><tr><td>API</td><td><a href="https://fiamma-api.node9x.com/">https://fiamma-api.node9x.com/</a></td></tr><tr><td>gRPC</td><td>fiamma-grpc.node9x.com:443</td></tr></tbody></table>

```bash
PEERS=$(curl -sS https://fiamma-rpc.node9x.com/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
echo $PEERS
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.fiamma/config/config.toml
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable fiammad
sudo systemctl restart fiammad && sudo journalctl -u fiammad -f
```

**➡️ Number of peers to which the node connects**

```bash
curl -Ss localhost:(whatever you typed in custom_port)657/net_info | jq .result.n_peers
```

**➡️ You can see the details of your peers with this code**

```bash
curl -sS http://localhost:(whatever you typed in custom_port)657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

### Latest Snapshot
height: **205959**, size: **102M**
```bash
sudo systemctl stop fiammad
cp $HOME/.fiamma/data/priv_validator_state.json $HOME/.fiamma/priv_validator_state.json.backup
rm -rf $HOME/.fiamma/data $HOME/.fiamma/wasm
curl curl -i https://snapshot.node9x.com/fiamma_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.fiamma
mv $HOME/.fiamma/priv_validator_state.json.backup $HOME/.fiamma/data/priv_validator_state.json
sudo systemctl restart fiammad && sudo journalctl -u fiammad -f
```
