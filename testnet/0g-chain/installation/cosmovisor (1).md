# Cosmovisor

#### 1. Stop node <a href="#id-1.-stop-node" id="id-1.-stop-node"></a>

```bash
sudo systemctl stop 0gd.service
```

#### 2. Download cosmovisor script to install <a href="#id-2.-download-cosmovisor-script-to-install" id="id-2.-download-cosmovisor-script-to-install"></a>

```bash
wget https://raw.githubusercontent.com/0glabs/0g-chain/dev/networks/testnet/init-cosmovisor.sh
```

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FZSOx43W7WmZIAs9OYhM8%252FScreen%2520Shot%25202024-08-03%2520at%252011.31.42.png%3Falt%3Dmedia%26token%3Dd91bc101-170d-4e5f-b430-50ac2e8bb9bd&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=da203861&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Check Script content `nano init-cosmovisor.sh`

```bash
#!/bin/bash

if [ -z "$1" ]; then
    echo "Usage: $0 <0G Home>"
    exit 1
fi

go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest

export DAEMON_NAME=0gchaind
echo "export DAEMON_NAME=0gchaind" >> ~/.profile
export DAEMON_HOME=$1
echo "export DAEMON_HOME=$1" >> ~/.profile
cosmovisor init $(whereis -b 0gchaind | awk '{print $2}')
mkdir $DAEMON_HOME/cosmovisor/backup
echo "export DAEMON_DATA_BACKUP_DIR=$DAEMON_HOME/cosmovisor/backup" >> ~/.profile
echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=true" >> ~/.profile
```

#### 3. Run script <a href="#id-3.-run-script" id="id-3.-run-script"></a>

_Before running this script, make sure Go is loaded in your current shell session. If you haven't loaded Go yet, please run the following command:_

```bash
source ~/.profile
```

```bash
chmod +x init-cosmovisor.sh
```

```bash
./init-cosmovisor.sh /root/.0gchain
```

Reload profile

```bash
source ~/.profile
```

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FDyFpWR0m41dbQB9XQoLG%252FScreen%2520Shot%25202024-08-03%2520at%252011.29.39.png%3Falt%3Dmedia%26token%3D0c436ed6-a971-40b4-b662-3ed5429772dc&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=c1c39fa8&#x26;sv=1" alt=""><figcaption></figcaption></figure>

#### 4. Edit service file to run with Cosmovisor <a href="#id-4.-edit-service-file-to-run-with-cosmovisor" id="id-4.-edit-service-file-to-run-with-cosmovisor"></a>

```bash
nano /etc/systemd/system/0gd.service
```

```bash
[Unit]
Description=Cosmovisor 0G Node
After=network.target

[Service]
User=root
Type=simple
ExecStart=/root/go/bin/cosmovisor run start
Restart=on-failure
LimitNOFILE=65535
Environment="DAEMON_NAME=0gchaind"
Environment="DAEMON_HOME=/root/.0gchain"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=true"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_DATA_BACKUP_DIR=/root/.0gchain/cosmovisor/backup"
Environment="UNSAFE_SKIP_BACKUP=true"

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload && \
sudo systemctl enable 0gd && \
sudo systemctl restart 0gd && sudo systemctl status 0gd
```

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FQNq7F5r4dFni2mn6bhXF%252FScreen%2520Shot%25202024-08-03%2520at%252011.31.02.png%3Falt%3Dmedia%26token%3Dbde1bf38-26b3-4ba8-bd3c-af32966785c3&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=4b0fe8b4&#x26;sv=1" alt=""><figcaption></figcaption></figure>

```bash
tail -f /root/.0gchain/log/chain.log
```
