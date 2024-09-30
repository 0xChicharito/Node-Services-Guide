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

## Stop node <a href="#stop-node" id="stop-node"></a>

```bash
sudo systemctl stop story
sudo systemctl stop story-geth
```

## Download Geth-data

*Latest snapshot: Mon, 30 Sep 2024 06:09:47 GMT | 87.96 GB*

```bash
wget -O geth_snapshot.lz4 https://snapshot.node9x.com/geth_snapshot.lz4
```

## Download Story-data

_Latest snapshot: Fri, 27 Sep 2024 06:08:43 GMT | 45.76 GB_

```bash
wget -O story_snapshot.lz4 https://snapshot.node9x.com/story_snapshot.lz4
```

## Backup `priv_validator_state.json`

```bash
mv $HOME/.story/story/data/priv_validator_state.json $HOME/.story/priv_validator_state.json.backup
```

## Remove old data <a href="#remove-old-data" id="remove-old-data"></a>

```bash
rm -rf ~/.story/story/data
rm -rf ~/.story/geth/iliad/geth/chaindata
```

## Extract Story-data <a href="#extract-story-data" id="extract-story-data"></a>

```bash
sudo mkdir -p /root/.story/story/data
lz4 -c -d story_snapshot.lz4 | tar -x -C /root/.story/story
```

## Extract Geth-data <a href="#extract-geth-data" id="extract-geth-data"></a>

```bash
sudo mkdir -p /root/.story/geth/iliad/geth/chaindata
lz4 -c -d geth_snapshot.lz4 | tar -x -C /root/.story/geth/iliad/geth
```

## Move `priv_validator_state.json` back <a href="#move-priv_validator_state.json-back" id="move-priv_validator_state.json-back"></a>

```bash
mv $HOME/.story/priv_validator_state.json.backup $HOME/.story/story/data/priv_validator_state.json
```

## Restart node <a href="#restart-node" id="restart-node"></a>

```bash
sudo systemctl start story
sudo systemctl start story-get
```
