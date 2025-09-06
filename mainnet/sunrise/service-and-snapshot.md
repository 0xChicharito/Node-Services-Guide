# 💾 Service & Snapshot

**Public Endpoint**

<table data-header-hidden><thead><tr><th width="133.33331298828125"></th><th></th></tr></thead><tbody><tr><td>RPC</td><td>https://sunrise-rpc.node9x.com</td></tr><tr><td>API</td><td>https://sunrise-api.node9x.com</td></tr></tbody></table>

**Live Peers**

```bash
PEERS=$(curl -sS https://sunrise-rpc.node9x.com/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
echo $PEERS
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.sunrise/config/config.toml
```

**Snapshot**\
height: **2986679**, size: **128G**

```bash
sudo systemctl stop sunrised
cp $HOME/.sunrise/data/priv_validator_state.json $HOME/.sunrise/priv_validator_state.json.backup
rm -rf $HOME/.sunrise/data
curl https://snapshot.node9x.com/sunrise_mainnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.sunrise
mv $HOME/.sunrise/priv_validator_state.json.backup $HOME/.sunrise/data/priv_validator_state.json
sudo systemctl restart sunrised && sudo journalctl -u sunrised -f
```
