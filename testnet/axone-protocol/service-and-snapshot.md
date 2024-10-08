# 💾 Service & Snapshot

## Public Endpoint <a href="#public-endpoint" id="public-endpoint"></a>

| RPC | [https://axone-rpc.node9x.com/](https://axone-rpc.node9x.com/) |
| --- | -------------------------------------------------------------- |
|     |                                                                |

## Live Peers <a href="#live-peers" id="live-peers"></a>

```bash
PEERS=$(curl -sS https://axone-rpc.node9x.com/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
echo $PEERS
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.axoned/config/config.toml
```

## Snapshot <a href="#snapshot" id="snapshot"></a>



```bash
sudo systemctl stop axoned cp $HOME/.axoned/data/priv_validator_state.json $HOME/.axoned/priv_validator_state.json.backup 
rm -rf $HOME/.axoned/data $HOME/.axoned/wasm 
curl https://snapshot.node9x.com/axone_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.axoned 
mv $HOME/.axoned/priv_validator_state.json.backup $HOME/.axoned/data/priv_validator_state.json 
sudo systemctl restart axoned && sudo journalctl -u axoned -f
```
