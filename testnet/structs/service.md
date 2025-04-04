# 💾 Service & Snapshot

**Public Endpoint**

| RPC      | [https://structs-rpc.node9x.com/](https://structs-rpc.node9x.com/) |
| -------- | ------------------------------------------------------------------ |
| gRPC     | structs-grpc.node9x.com:443                                        |
| Chain ID | structstestnet-100                                                 |

**Live Peers**

```
PEERS=$(curl -sS https://structs-rpc.node9x.com/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
echo $PEERS
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.structs/config/
```

**Snapshot**

```bash
sudo systemctl stop exrpd
cp $HOME/.exrpd/data/priv_validator_state.json $HOME/.exrpd/priv_validator_state.json.backup
rm -rf $HOME/.exrpd/data
curl https://snapshot.node9x.com/xrplvm_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.exrpd
mv $HOME/.exrpd/priv_validator_state.json.backup $HOME/.exrpd/data/priv_validator_state.json
sudo systemctl restart exrpd && sudo journalctl -u exrpd -f
```
