# 💾 Snapshot

_Latest snapshot: Tue, 03 Sep 2024 11:48:38 GMT | 0.00 GB_

```bash
cd $HOME
snap install lz4
sudo systemctl stop fiammad
cp $HOME/.fiamma/data/priv_validator_state.json $HOME/.fiamma/priv_validator_state.json.backup
fiammad tendermint unsafe-reset-all --home $HOME/.fiamma --keep-addr-book
curl -o - -L https://snapshot.node9x.com/fiamma-snap.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.fiamma --strip-components 2
mv $HOME/.fiamma/priv_validator_state.json.backup $HOME/.fiamma/data/priv_validator_state.json
sudo systemctl restart fiammad && journalctl -fu fiammad -o cat
```
