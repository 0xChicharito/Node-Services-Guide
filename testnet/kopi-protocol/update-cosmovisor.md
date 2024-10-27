---
hidden: true
---

# 🪢 Update Cosmovisor

#### Configure Cosmovisor:

```bash
cd $HOME
mkdir -p $HOME/.kopid/cosmovisor/genesis/bin
mkdir -p $HOME/.kopid/cosmovisor/upgrades
cp $HOME/go/bin/kopid $HOME/.kopid/cosmovisor/genesis/bin/
```

#### Download and install Cosmovisor

```bash
cd $HOME
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@lates
```
