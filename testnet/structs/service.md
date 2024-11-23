# 💾 Service

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
