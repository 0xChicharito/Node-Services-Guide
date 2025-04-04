# 💾 Service & Snapshot

**Public Endpoint**

<table data-header-hidden><thead><tr><th width="133.33331298828125"></th><th></th></tr></thead><tbody><tr><td>RPC</td><td><a href="https://xrpl-rpc.node9x.com/">https://xrpl-rpc.node9x.com/ </a></td></tr><tr><td>API</td><td><a href="https://xrpl-api.node9x.com/">https://xrpl-api.node9x.com/</a></td></tr><tr><td>JSON-RPC</td><td><a href="https://xrpl-evm.node9x.com/">https://xrpl-evm.node9x.com/</a></td></tr></tbody></table>

**Live Peers**

```bash
PEERS=$(curl -sS 
https://xrpl-rpc.node9x.com/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
echo $PEERS
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS
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
