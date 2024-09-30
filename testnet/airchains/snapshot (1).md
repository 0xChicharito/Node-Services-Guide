# Snapshot

_Latest snapshot: Tue, 03 Sep 2024 05:46:55 GMT | 544.97 GB_

```bash
sudo systemctl stop junctiond
cp $HOME/.junction/data/priv_validator_state.json $HOME/.junction/priv_validator_state.json.backup
rm -rf $HOME/.junction/data 
curl https://snapshot.node9x.com/airchains-testnet_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.junction
mv $HOME/.junction/priv_validator_state.json.backup $HOME/.junction/data/priv_validator_state.json
sudo systemctl restart junctiond && sudo journalctl -u junctiond -f
```

```
```
