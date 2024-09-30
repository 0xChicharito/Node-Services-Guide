# ⛓️ Installation

### 1. Update and install packages <a href="#id-1.-update-and-install-packages" id="id-1.-update-and-install-packages"></a>

```bash
sudo apt update & sudo apt upgrade -y
sudo apt install ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev curl git wget make jq build-essential pkg-config lsb-release libssl-dev libreadline-dev libffi-dev gcc screen unzip lz4 -y
```

### 2. Install Docker & Docker compose <a href="#id-2.-install-docker-and-docker-compose" id="id-2.-install-docker-and-docker-compose"></a>

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
docker version
```

```bash
VER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)
curl -L "https://github.com/docker/compose/releases/download/"$VER"/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

### 3. Install Go <a href="#id-3.-install-go" id="id-3.-install-go"></a>

```bash
cd $HOME && \
ver="1.22.0" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile && \
source ~/.bash_profile && \
go version
```

### 4. Install EigenLayer CLI <a href="#id-4.-install-eigenlayer-cli" id="id-4.-install-eigenlayer-cli"></a>

```bash
curl -sSfL https://raw.githubusercontent.com/layr-labs/eigenlayer-cli/master/scripts/install.sh | sh -s
export PATH=$PATH:~/bin
eigenlayer --version
```

### 5. Clone Chainbase AVS repo <a href="#id-5.-clone-chainbase-avs-repo" id="id-5.-clone-chainbase-avs-repo"></a>

```bash
git clone https://github.com/chainbase-labs/chainbase-avs-setup
cd chainbase-avs-setup/holesky
```

### 6. Create/Import Eigenlayer wallet <a href="#id-6.-create-import-eigenlayer-wallet" id="id-6.-create-import-eigenlayer-wallet"></a>

```bash
eigenlayer operator keys create --key-type ecdsa "wallet_name"
```

Set password & save your private key:

![](https://service.josephtran.xyz/\~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FAnIDFpkPRXypmaxzAOnI%252FScreen%2520Shot%25202024-08-07%2520at%252016.48.16.png%3Falt%3Dmedia%26token%3Da4fad3c3-2481-43cf-9ef2-376c727fdddb\&width=768\&dpr=4\&quality=100\&sign=d308d030\&sv=1)

#### # Import an old key (optional) <a href="#import-an-old-key-optional" id="import-an-old-key-optional"></a>

```bash
eigenlayer operator keys import --key-type ecdsa "wallet_name" PRIVATEKEY
```

### 7. Fund your Eigen wallet <a href="#id-7.-fund-your-eigen-wallet" id="id-7.-fund-your-eigen-wallet"></a>

You’ll need at least 1 Holesky ETH to cover the gas cost of the operator registration. Make sure to send at least 1 ETH to your Eigenlayer operator’s address

### 8. Config & register operator <a href="#id-8.-config-and-register-operator" id="id-8.-config-and-register-operator"></a>

```bash
eigenlayer operator config create
```

* Enter your operator address: `your Eigenlayer address`
* Enter your earnings address (default to your operator address): \``` your Eigenlayer address` ``
* Enter your ETH rpc url: `https://ethereum-holesky-rpc.publicnode.com`
* Select your network: \`holesky\`
* Select your signer type: \`local\_keystore\`
* Enter your ecdsa key path: `/root/.eigenlayer/operator_keys/joseph-test.ecdsa.key.json`

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252F2dlXtD6YWKIOtIqRQl8f%252FScreen%2520Shot%25202024-08-07%2520at%252016.57.11.png%3Falt%3Dmedia%26token%3D146d3355-937d-4745-93bf-e31f5c9b295f&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=8e0412a9&#x26;sv=1" alt=""><figcaption></figcaption></figure>

### 9. Edit metadata.json <a href="#id-9.-edit-metadata.json" id="id-9.-edit-metadata.json"></a>

```bash
nano /root/chainbase-avs-setup/holesky/metadata.json
```

Sample:

```bash
{
  "name": "Chicharito",
  "website": "https://www.node9x.com",
  "description": "Blockchain enthusiast and validator",
  "logo": "https://raw.githubusercontent.com/0xChicharito/chainbase/main/Jlogo.png",
  "twitter": "https://x.com/0xchicharito"
}
```

Logo support .png only and less than 1Mb

* Create a Public repositry in github
* Upload `logo.png` and `metadata.json`there.

### 10. Edit operator.yaml <a href="#id-10.-edit-operator.yaml" id="id-10.-edit-operator.yaml"></a>

```bash
nano /root/chainbase-avs-setup/holesky/operator.yaml
```

Set URL metadatar with raw file link:

* metadata\_url: `https://raw.githubusercontent.com/0xChicharito/chainbase/main/metadata.json`

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252Fh3CT4muJXeL4ChQsMr8u%252FScreen%2520Shot%25202024-08-07%2520at%252017.10.58.png%3Falt%3Dmedia%26token%3D0f5bb157-4a23-4357-85cb-af027ab0e934&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=3898edef&#x26;sv=1" alt=""><figcaption></figcaption></figure>

### 11. Run Eigenlayer holesky Node <a href="#id-11.-run-eigenlayer-holesky-node" id="id-11.-run-eigenlayer-holesky-node"></a>

```bash
eigenlayer operator register operator.yaml
```

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FJXAulifitZW5SIp028NO%252FScreen%2520Shot%25202024-08-07%2520at%252017.20.36.png%3Falt%3Dmedia%26token%3D1432576c-d841-4917-a7ee-3fa12df21f0b&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=d8a01155&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Check status:

```bash
eigenlayer operator status operator.yaml
```

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FcgMfftyVZscQzOagiCe6%252FScreen%2520Shot%25202024-08-07%2520at%252017.21.05.png%3Falt%3Dmedia%26token%3Dc3849e9b-b860-455d-8ce0-1d69f90548e1&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=c7b54ad6&#x26;sv=1" alt=""><figcaption></figcaption></figure>

### 12. Config Chainbase AVS <a href="#id-12.-config-chainbase-avs" id="id-12.-config-chainbase-avs"></a>

#### a. **Create .env file** <a href="#a.-create-.env-file" id="a.-create-.env-file"></a>

```
nano .env
```

Input content and edit your info:

```bash
# Chainbase AVS Image
MAIN_SERVICE_IMAGE=repository.chainbase.com/network/chainbase-node:testnet-v0.1.7
FLINK_TASKMANAGER_IMAGE=flink:latest
FLINK_JOBMANAGER_IMAGE=flink:latest
PROMETHEUS_IMAGE=prom/prometheus:latest

MAIN_SERVICE_NAME=chainbase-node
FLINK_TASKMANAGER_NAME=flink-taskmanager
FLINK_JOBMANAGER_NAME=flink-jobmanager
PROMETHEUS_NAME=prometheus

# FLINK CONFIG
FLINK_CONNECT_ADDRESS=flink-jobmanager
FLINK_JOBMANAGER_PORT=8081
NODE_PROMETHEUS_PORT=9091
PROMETHEUS_CONFIG_PATH=./prometheus.yml

# Chainbase AVS mounted locations
NODE_APP_PORT=8080
NODE_ECDSA_KEY_FILE=/app/operator_keys/ecdsa_key.json
NODE_LOG_DIR=/app/logs

# Node logs configs
NODE_LOG_LEVEL=debug
NODE_LOG_FORMAT=text

# Metrics specific configs
NODE_ENABLE_METRICS=true
NODE_METRICS_PORT=9092

# holesky smart contracts
AVS_CONTRACT_ADDRESS=0x5E78eFF26480A75E06cCdABe88Eb522D4D8e1C9d
AVS_DIR_CONTRACT_ADDRESS=0x055733000064333CaDDbC92763c58BF0192fFeBf

###############################################################################
####### TODO: Operators please update below values for your node ##############
###############################################################################
# TODO: Operators need to point this to a working chain rpc
NODE_CHAIN_RPC=https://rpc.ankr.com/eth_holesky
NODE_CHAIN_ID=17000

# TODO: Operators need to update this to their own paths
USER_HOME=$HOME
EIGENLAYER_HOME=${USER_HOME}/.eigenlayer
CHAINBASE_AVS_HOME=${EIGENLAYER_HOME}/chainbase/holesky

NODE_LOG_PATH_HOST=${CHAINBASE_AVS_HOME}/logs

# TODO: Operators need to update this to their own keys
NODE_ECDSA_KEY_FILE_HOST=${EIGENLAYER_HOME}/operator_keys/joseph-test.ecdsa.key.json

# TODO: Operators need to add password to decrypt the above keys
# If you have some special characters in password, make sure to use single quotes
NODE_ECDSA_KEY_PASSWORD=***123ABCabc123***
```

Update:

`NODE_ECDSA_KEY_FILE_HOST`=${EIGENLAYER\_HOME}/operator\_keys/joseph-test.ecdsa.key.json `NODE_ECDSA_KEY_PASSWORD`="your password" `NODE_CHAIN_RPC`=https://rpc.ankr.com/eth\_holesky

#### b. **Create `docker-compose.yml` file** <a href="#b.-create-docker-compose.yml-file" id="b.-create-docker-compose.yml-file"></a>

Copy

```bash
# Remove old file
rm -rf docker-compose.yml
# Open edit menu
nano docker-compose.yml
```

Input this content. Change port `8081, 9091, 8080, 9092` if you want.

```bash
services:
  prometheus:
    image: ${PROMETHEUS_IMAGE}
    container_name: ${PROMETHEUS_NAME}
    env_file:
      - .env
    volumes:
      - "${PROMETHEUS_CONFIG_PATH}:/etc/prometheus/prometheus.yml"
    command: 
      - "--enable-feature=expand-external-labels"
      - "--config.file=/etc/prometheus/prometheus.yml"
    ports:
      - "9091:9090"
    networks:
      - chainbase
    restart: unless-stopped

  flink-jobmanager:
    image: ${FLINK_JOBMANAGER_IMAGE}
    container_name: ${FLINK_JOBMANAGER_NAME}
    env_file:
      - .env
    ports:
      - "8081:8081"
    command: jobmanager
    networks:
      - chainbase
    restart: unless-stopped

  flink-taskmanager:
    image: ${FLINK_JOBMANAGER_IMAGE}
    container_name: ${FLINK_TASKMANAGER_NAME}
    env_file:
      - .env
    depends_on:
      - flink-jobmanager
    command: taskmanager
    networks:
      - chainbase
    restart: unless-stopped

  chainbase-node:
    image: ${MAIN_SERVICE_IMAGE}
    container_name: ${MAIN_SERVICE_NAME}
    command: ["run"]
    env_file:
      - .env
    ports:
      - "8080:8080"
      - "9092:9092"
    volumes:
      - "${NODE_ECDSA_KEY_FILE_HOST:-./opr.ecdsa.key.json}:${NODE_ECDSA_KEY_FILE}"
      - "${NODE_LOG_PATH_HOST}:${NODE_LOG_DIR}:rw"
    depends_on:
      - prometheus
      - flink-jobmanager
      - flink-taskmanager
    networks:
      - chainbase
    restart: unless-stopped

networks:
  chainbase:
    driver: bridge
```

#### **c. Create folders for docker** <a href="#c.-create-folders-for-docker" id="c.-create-folders-for-docker"></a>

```bash
source .env && mkdir -pv ${EIGENLAYER_HOME} ${CHAINBASE_AVS_HOME} ${NODE_LOG_PATH_HOST}
```

**Set permissions to bash script** _chainbase-avs.sh_

```bash
chmod +x ./chainbase-avs.sh
```

#### d. **Edit prometheus.yml** <a href="#d.-edit-prometheus.yml" id="d.-edit-prometheus.yml"></a>

```bash
nano prometheus.yml
```

Input your operator address

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FIcQHoWnTpbZwU0DlCB0F%252FScreen%2520Shot%25202024-08-07%2520at%252017.09.27.png%3Falt%3Dmedia%26token%3Dad668642-14bd-4eae-ba40-94168ed4edbf&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=c183c4a2&#x26;sv=1" alt=""><figcaption></figcaption></figure>

### 13. Register & Run Chainbase AVS <a href="#id-13.-register-and-run-chainbase-avs" id="id-13.-register-and-run-chainbase-avs"></a>

```bash
./chainbase-avs.sh register
```

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FVyqD1CRqB1Pd4UJAzaKs%252FScreen%2520Shot%25202024-08-07%2520at%252017.41.50.png%3Falt%3Dmedia%26token%3D62c29481-73b6-45f9-9dfd-d0878b87a76f&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=97cb2ab7&#x26;sv=1" alt=""><figcaption></figcaption></figure>

```bash
./chainbase-avs.sh run
```

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FfC0qQY7owl8HsyTie70O%252FScreen%2520Shot%25202024-08-07%2520at%252014.42.31.png%3Falt%3Dmedia%26token%3D8acf30f6-d70e-42c2-9e02-925172f2d483&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=621f1e7b&#x26;sv=1" alt=""><figcaption></figcaption></figure>

### 14. Check AVS health <a href="#id-14.-check-avs-health" id="id-14.-check-avs-health"></a>

```bash
docker compose logs chainbase-node -f
```

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FC9TvdxNkN1rV1WRRf4XH%252FScreen%2520Shot%25202024-08-07%2520at%252017.48.40.png%3Falt%3Dmedia%26token%3Dc6fe11f2-dea9-4415-bf6b-6e464fdaa9ed&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=b48306b4&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Get AVS link

```bash
export PATH=$PATH:~/bin

eigenlayer operator status operator.yaml
```

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FMOG33VLAnp4zbSMnncQ2%252FScreen%2520Shot%25202024-08-07%2520at%252017.21.05.png%3Falt%3Dmedia%26token%3D704ae826-38a1-43cb-9822-e61b540052a5&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=b963aca7&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Check **Operator Health**

```bash
# If your port is 8080
curl -i localhost:8080/eigen/node/health
```

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FzBKIuKlbYcxaqa62QXbP%252FScreen%2520Shot%25202024-08-07%2520at%252017.49.04.png%3Falt%3Dmedia%26token%3D10716244-e822-4715-a3a4-a8648d724cc7&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=75117a84&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Check Docker containers

```bash
docker ps
```

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FJTSsID1iqJqnNjWiWdRQ%252FScreen%2520Shot%25202024-08-07%2520at%252017.52.05.png%3Falt%3Dmedia%26token%3D560ff425-5e98-4ac6-b836-f7bf52070996&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=512b19a6&#x26;sv=1" alt=""><figcaption></figcaption></figure>
