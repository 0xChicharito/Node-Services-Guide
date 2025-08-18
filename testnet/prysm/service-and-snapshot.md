# 🛰️ Service & Snapshot

### Public Endpoint <a href="#public-endpoint" id="public-endpoint"></a>

| RPC       | [https://prysm-rpc.node9x.com](https://prysm-rpc.node9x.com) |
| --------- | ------------------------------------------------------------ |
| gRPC      | prysm-grpc.node9x.com:443                                    |
| Websocket | wss://prysm-rpc.node9x.com/websocket                         |

### Peer <a href="#peer" id="peer"></a>

```
170bf5fa23b18d19148ca9a52dbdde485ad59f7b@65.109.79.185:15656
```

```bash
curl -s localhost:15657/status | jq -r '.result.node_info | "\(.id)@'"$(curl -4 -s ifconfig.me)"':\(.listen_addr | split(":")[-1])"'
```
height: **16237121**, size: **46M**
### Live Peers <a href="#live-peers" id="live-peers"></a>

```
PEERS=$(curl -sS https://prysm-rpc.node9x.com/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
echo $PEERS
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.prysm/config/config.toml
```

### Snapshot <a href="#snapshot" id="snapshot"></a>
height: **3419092**, size: **20G**
```bash
sudo systemctl stop prysmd
cp $HOME/.prysm/data/priv_validator_state.json $HOME/.prysm/priv_validator_state.json.backup
rm -rf $HOME/.prysm/data $HOME/.prysm/wasm
curl https://snapshot.node9x.com/prysm_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.prysm
mv $HOME/.prysm/priv_validator_state.json.backup $HOME/.prysm/data/priv_validator_state.json
sudo systemctl restart prysmd && sudo journalctl -u prysmd -f
```
