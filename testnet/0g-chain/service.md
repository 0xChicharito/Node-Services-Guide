# Service & Snapshot

| RPC     | [https://0gchain-rpc.node9x.com/](https://0gchain-rpc.node9x.com/)         |
| ------- | -------------------------------------------------------------------------- |
| API     | [https://0gchain-api.node9x.com/](https://0gchain-api.node9x.com/)         |
| EVM RPC | [https://0gchain-evm-rpc.node9x.com/](https://0gchain-evm-rpc.node9x.com/) |

**Connections**

Peer

```
2625169af3c12c7ce5275454741004b8980bf6da@65.21.97.150:47656
```

*Latest snapshot: Sun, 10 Nov 2024 08:31:26 GMT | 7.76 GB*

```bash
sudo systemctl stop 0gchaind
cp $HOME/.0gchain/data/priv_validator_state.json $HOME/.0gchain/priv_validator_state.json.backup
rm -rf $HOME/.0gchain/data 
curl https://snapshot.node9x.com/og_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.0gchain
mv $HOME/.0gchain/priv_validator_state.json.backup $HOME/.0gchain/data/priv_validator_state.json
sudo systemctl restart 0gchaind && sudo journalctl -u 0gchaind -f
```
