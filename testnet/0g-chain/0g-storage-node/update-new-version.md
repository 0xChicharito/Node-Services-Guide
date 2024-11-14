# Upgrade (v0.7.3)

### 1. Stop node <a href="#id-1.-stop-node" id="id-1.-stop-node"></a>

```bash
sudo systemctl stop zgs
```

### 2. Backup config file <a href="#id-2.-backup-config-file" id="id-2.-backup-config-file"></a>

```bash
mv $HOME/0g-storage-node/run/config-testnet-turbo.toml $HOME/config-testnet-turbo_backup.toml
```

### 3. Clone and build new binary <a href="#id-3.-clone-and-build-new-binary" id="id-3.-clone-and-build-new-binary"></a>

```bash
cd $HOME/0g-storage-node
git fetch --all --tags
git checkout v0.7.3
git submodule update --init
cargo build --release
```

### 4. Move config file back <a href="#id-4.-move-config-file-back" id="id-4.-move-config-file-back"></a>

```bash
mv $HOME/config-testnet-turbo_backup.toml $HOME/0g-storage-node/run/config-testnet-turbo.toml
```

### 5. Restart node <a href="#id-5.-restart-node" id="id-5.-restart-node"></a>

```bash
sudo systemctl restart zgs && sudo systemctl status zgs
```

### 6. Check your version <a href="#id-5.-restart-node" id="id-5.-restart-node"></a>

```bash
$HOME/0g-storage-node/target/release/zgs_node --version
```
