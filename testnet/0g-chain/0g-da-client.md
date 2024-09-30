---
description: 'Recommended Hardware: 2 Cores, 8GB RAM, 100Gb of storage (NVME)'
---

# ☑️ 0G DA Client

### **System updates, installation of required dependencies** <a href="#system-updates-installation-of-required-dependencies" id="system-updates-installation-of-required-dependencies"></a>

```
sudo apt-get update
sudo apt-get install cmake 
```

Install Go

```
wget https://go.dev/dl/go1.22.0.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.22.0.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
```

Installation

```
cd $HOME
git clone -b v1.0.0-testnet https://github.com/0glabs/0g-da-client.git
cd $HOME/0g-da-client
make build
```

**Edit file Makefile**

```
nano $HOME/0g-da-client/disperser/Makefile
```

Refer

```
cd disperser
screen 
./bin/combined \
    --chain.rpc http://<your validator IP>:8545 \
    --chain.private-key <validator Private-key> \
    --chain.receipt-wait-rounds 180 \
    --chain.receipt-wait-interval 1s \
    --chain.gas-limit 2000000 \
    --combined-server.use-memory-db \
    --combined-server.storage.kv-db-path ./root/0g-storage-kv/run \
    --combined-server.storage.time-to-expire 300 \
    --disperser-server.grpc-port 51001 \
    --batcher.da-entrance-contract 0xDFC8B84e3C98e8b550c7FEF00BCB2d8742d80a69 \
    --batcher.da-signers-contract 0x0000000000000000000000000000000000001000 \
    --batcher.finalizer-interval 20s \
    --batcher.confirmer-num 3 \
    --batcher.max-num-retries-for-sign 3 \
    --batcher.finalized-block-count 50 \
    --batcher.batch-size-limit 500 \
    --batcher.encoding-interval 3s \
    --batcher.encoding-request-queue-size 1 \
    --batcher.pull-interval 10s \
    --batcher.signing-interval 3s \
    --batcher.signed-pull-interval 20s \
    --encoder-socket <your DA encoder IP>:34000 \
    --encoding-timeout 600s \
    --signing-timeout 600s \
    --chain-read-timeout 12s \
    --chain-write-timeout 13s \
    --combined-server.log.level-file trace \
    --combined-server.log.level-std  trace \
```

Run

```
make run_combined
```
