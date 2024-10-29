# 💾 Service & Snapshot

## Public Endpoint

|               |                                                                              |
| ------------- | ---------------------------------------------------------------------------- |
| RPC           | [https://rpc-story.node9x.com/](https://rpc-story.node9x.com/)               |
| EVM RPC       | [https://evm-rpc-story.node9x.com/](https://evm-rpc-story.node9x.com/)       |
| API           | [https://api-story.node9x.com/](https://api-story.node9x.com/)               |
| Websocket     | [wss://rpc-story.node9x.com/websocket](wss://rpc-story.node9x.com/websocket) |
| EVM Websocket | [wss://wss-story.node9x.com/](wss://wss-story.node9x.com/)                   |

## Peer

```bash
curl -s localhost:52657/status | jq -r '.result.node_info | "\(.id)@'"$(curl -4 -s ifconfig.me)"':\(.listen_addr | split(":")[-1])"'
```

## Live Peers

```bash
PEERS=$(curl -sS https://rpc-story.node9x.com/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
echo $PEERS
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.story/story/config/config.toml
```

## Snapshot

SOON
