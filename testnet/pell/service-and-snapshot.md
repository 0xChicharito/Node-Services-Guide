# 💾 Service & Snapshot

### Public Endpoint <a href="#public-endpoint" id="public-endpoint"></a>

| RPC | [https://pell-rpc.node9x.com/](https://pell-rpc.node9x.com/) |
| --- | ------------------------------------------------------------ |
| API | [https://pell-api.node9x.com/](https://pell-api.node9x.com/) |

### Live Peers <a href="#live-peers" id="live-peers"></a>

```bash
PEERS=$(curl -sS https://pell-rpc.node9x.com/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
echo $PEERS
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.pellcored/config/config.toml
```

### Snapshot <a href="#snapshot" id="snapshot"></a>

height: **1398584**, size: **6.6G**

```bash
udo systemctl stop pellcored
cp $HOME/.pellcored/data/priv_validator_state.json $HOME/.pellcored/priv_validator_state.json.backup 
rm -rf $HOME/.pellcored/data $HOME/.pellcored/wasm 
curl https://snapshot.node9x.com/pell_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.pellcored 
mv $HOME/.pellcored/priv_validator_state.json.backup $HOME/.pellcored/data/priv_validator_state.json 
sudo systemctl restart pellcored && sudo journalctl -u pellcored -f
```
