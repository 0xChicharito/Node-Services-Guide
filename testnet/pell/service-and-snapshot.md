# 💾 Service & Snapshot

### Public Endpoint <a href="#public-endpoint" id="public-endpoint"></a>

<table data-header-hidden><thead><tr><th width="134"></th><th></th></tr></thead><tbody><tr><td>RPC</td><td><a href="https://pell-rpc.node9x.com/">https://pell-rpc.node9x.com/</a></td></tr><tr><td>API</td><td><a href="https://pell-api.node9x.com/">https://pell-api.node9x.com/</a></td></tr></tbody></table>

### Live Peers <a href="#live-peers" id="live-peers"></a>

```bash
PEERS=$(curl -sS https://pell-rpc.node9x.com/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
echo $PEERS
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.pellcored/config/config.toml
```

### Snapshot <a href="#snapshot" id="snapshot"></a>

height: **3473169**, size: **1.9G**

```bash
sudo systemctl stop pellcored 
cp $HOME/.pellcored/data/priv_validator_state.json $HOME/.pellcored/priv_validator_state.json.backup 
rm -rf $HOME/.pellcored/data $HOME/.pellcored/wasm 
curl https://snapshot.node9x.com/pell_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.pellcored 
mv $HOME/.pellcored/priv_validator_state.json.backup $HOME/.pellcored/data/priv_validator_state.json 
sudo systemctl restart pellcored && sudo journalctl -u pellcored -f
```
