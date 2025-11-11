# 💾 Service & Snapshot

Public Endpoint

<table><thead><tr><th width="110.66668701171875"></th><th></th></tr></thead><tbody><tr><td>RPC</td><td><a href="https://airchains-rpc.node9x.com">https://airchains-rpc.node9x.com</a></td></tr><tr><td>API</td><td><a href="https://airchains-api.node9x.com">https://airchains-api.node9x.com</a></td></tr></tbody></table>

## Snapshot

height: **3534310**, size: **321M**

```bash
sudo systemctl stop junctiond
cp $HOME/.junctiond/data/priv_validator_state.json $HOME/.junctiond/priv_validator_state.json.backup
rm -rf $HOME/.junctiond/data 
curl https://snapshot.node9x.com/airchains_testnet.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.junctiond
mv $HOME/.junctiond/priv_validator_state.json.backup $HOME/.junctiond/data/priv_validator_state.json
sudo systemctl restart junctiond && sudo journalctl -u junctiond 
```
