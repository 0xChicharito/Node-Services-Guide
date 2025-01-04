# 💾 Service & Snapshot

## Public Endpoint

|               |                                                                              |
| ------------- | ---------------------------------------------------------------------------- |
| RPC           | [https://rpc-story.node9x.com/](https://rpc-story.node9x.com/)               |
| EVM RPC       | [https://evm-rpc-story.node9x.com/](https://evm-rpc-story.node9x.com/)       |
| API           | [https://api-story.node9x.com/](https://api-story.node9x.com/)               |
| Websocket     | [wss://rpc-story.node9x.com/websocket](wss://rpc-story.node9x.com/websocket) |
| EVM Websocket | [wss://wss-story.node9x.com/](wss://wss-story.node9x.com/)                   |

## Live Peers

```bash
PEERS=$(curl -sS https://rpc-story.node9x.com/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
echo $PEERS
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.story/story/config/config.toml
```

## Snapshot

height: **1753395**, size: **207G**

```bash
# install dependencies, and disable statesync to avoid sync issues
sudo apt install curl tmux jq lz4 unzip aria2 -y
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.story/story/config/config.toml

# stop node, download Story and Geth snapshot
cd $HOME
sudo systemctl stop story story-geth
aria2c -x 16 -s 16 -o story-archive-snap.tar.lz4 https://snapshot.node9x.com/story_testnet.tar.lz4
aria2c -x 16 -s 16 -o geth-archive-snap.tar.lz4 https://snapshot.node9x.com/geth_story_testnet.tar.lz4

# backup priv_validator_state.json, remove data, unpack Story snapshot and restore priv_validator_state.json
cp $HOME/.story/story/data/priv_validator_state.json $HOME/.story/story/priv_validator_state.json.backup
rm -rf $HOME/.story/story/data
tar -I lz4 -xvf ~/story-archive-snap.tar.lz4 -C $HOME/.story/story
mv $HOME/.story/story/priv_validator_state.json.backup $HOME/.story/story/data/priv_validator_state.json

# delete geth data and unpack Geth snapshot
rm -rf $HOME/.story/geth/odyssey/geth/chaindata
tar -I lz4 -xvf ~/geth-archive-snap.tar.lz4 -C $HOME/.story/geth/odyssey/geth

# restart node and check logs
sudo systemctl restart story story-geth
sudo journalctl -u story-geth -u story -f
```
