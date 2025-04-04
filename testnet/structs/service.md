# 💾 Service & Snapshot

**Public Endpoint**

<table data-header-hidden><thead><tr><th width="133.33331298828125"></th><th></th></tr></thead><tbody><tr><td>RPC</td><td><a href="https://structs-rpc.node9x.com/">https://structs-rpc.node9x.com/</a></td></tr><tr><td>gRPC</td><td>structs-grpc.node9x.com:443</td></tr><tr><td>Chain ID</td><td>structstestnet-100</td></tr></tbody></table>

**Live Peers**

```
PEERS=$(curl -sS https://structs-rpc.node9x.com/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
echo $PEERS
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.structs/config/
```

