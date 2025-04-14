---
cover: ../../.gitbook/assets/1080x360.jpg
coverY: 0
---

# 💾 Service

### Public Endpoint <a href="#public-endpoint" id="public-endpoint"></a>

| RPC | [https://zenrock-rpc.node9x.com/](https://zenrock-rpc.node9x.com/) |
| --- | ------------------------------------------------------------------ |
|     |                                                                    |

### Peer <a href="#peer" id="peer"></a>

```
33b51bee7940de677e26e10fc6d37cab7f5d3372@65.109.79.185:11656
```

```
curl -s localhost:11657/status | jq -r '.result.node_info | "\(.id)@'"$(curl -4 -s ifconfig.me)"':\(.listen_addr | split(":")[-1])"'
```

### Live Peers <a href="#live-peers" id="live-peers"></a>

```
PEERS=$(curl -sS https://zenrock-rpc.node9x.com/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
echo $PEERS
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.zrchain/config/config.toml
```

### Snapshot <a href="#snapshot" id="snapshot"></a>
height: **792962**, size: **4.6G**


```bash
sudo systemctl stop zenrockd cp $HOME/.zrchain/data/priv_validator_state.json $HOME/.zrchain/priv_validator_state.json.backup 
rm -rf $HOME/.zrchain/data $HOME/.zrchain/wasm 
curl https://snapshot.node9x.com/zenrock_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.zrchain 
mv $HOME/.zrchain/priv_validator_state.json.backup $HOME/.zrchain/data/priv_validator_state.json 
sudo systemctl restart zenrockd && sudo journalctl -u zenrockd -f
```
