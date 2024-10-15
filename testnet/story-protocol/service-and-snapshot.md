# 💾 Service & Snapshot

## Public Endpoint

|             |                                                                      |
| ----------- | -------------------------------------------------------------------- |
| RPC         | [https://story-rpc.node9x.com/](https://story-rpc.node9x.com/)       |
| EVM RPC     | [https://story-evm-rpc.node9x.com](https://story-evm-rpc.node9x.com) |
| <p><br></p> |                                                                      |

## Peer

```bash
d4c5dcfbec11d80399bcf18d83a157259ca3efc7@story-peer.node9x.com:26656
```

```bash
curl -s localhost:26657/status | jq -r '.result.node_info | "\(.id)@'"$(curl -4 -s ifconfig.me)"':\(.listen_addr | split(":")[-1])"'
```

## Live Peers

```bash
PEERS=$(curl -sS https://story-rpc.node9x.com/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
echo $PEERS
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.story/story/config/config.toml
```

## Snapshot
*Latest snapshot: Mon, 14 Oct 2024 19:16:16 GMT | 383.59 GB*


```bash
sudo systemctl stop story
sudo systemctl stop story-geth
cp $HOME/.story/story/data/priv_validator_state.json $HOME/.story/story/priv_validator_state.json.backup
rm -rf $HOME/.story/story/data
rm -rf $HOME/.story/geth/iliad/geth/chaindata
curl https://snapshot.node9x.com/story_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.story
mv $HOME/.story/story/priv_validator_state.json.backup $HOME/.story/story/data/priv_validator_state.json
sudo systemctl start story-geth
sudo systemctl start story
sudo journalctl -u story -f
```
