# 💾 Service & Snapshot

#### Public Endpoint <a href="#public-endpoint" id="public-endpoint"></a>

| RPC  | [https://kopi-rpc.node9x.com/](https://kopi-rpc.node9x.com/) |
| ---- | ------------------------------------------------------------ |
| gRPC | kopi-grpc.node9x.com:443                                     |

#### Live Peers <a href="#live-peers" id="live-peers"></a>

```bash
PEERS=$(curl -sS https://kopi-rpc.node9x.com/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
echo $PEERS
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.kopid/config/config.toml
```

#### Snapshot <a href="#snapshot" id="snapshot"></a>

```bash
sudo systemctl stop kopid
cp $HOME/.kopid/data/priv_validator_state.json $HOME/.kopid/priv_validator_state.json.backup
rm -rf $HOME/.kopid/data $HOME/.kopid/wasm
curl https://snapshot.node9x.com/kopi-snapshot.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.prysm
mv $HOME/.kopid/priv_validator_state.json.backup $HOME/.kopid/data/priv_validator_state.json
sudo systemctl restart kopid && sudo journalctl -u kopid -f
```
