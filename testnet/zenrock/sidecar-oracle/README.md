# ⚙️ Sidecar (✔️Oracle)

The validator sidecar service allows validators to vote on oracle data during the CometBFT consensus process

## **Install the sidecar**

```bash
# create the necessary directories
mkdir -p $HOME/.zrchain/sidecar/bin
mkdir -p $HOME/.zrchain/sidecar/keys
```

## **Install the binary file**

```bash
wget -O $HOME/.zrchain/sidecar/bin/zenrock-sidecar https://releases.gardia.zenrocklabs.io/validator_sidecar-1.2.3
chmod +x $HOME/.zrchain/sidecar/bin/zenrock-sidecar
```

## **Copy zenrock-validators**

```bash
cd $HOME
git clone https://github.com/zenrocklabs/zenrock-validators
```

## **Set a password for sidecar wallets**

```bash
read -p "Enter password for the keys: " key_pass
```

Sử dụng lệnh `read` trong `zsh`

```bash
echo -n "Enter password for the keys: "
read key_pass
```

## **Build BLS binary**

```bash
cd $HOME/zenrock-validators/utils/keygen/bls/
go mod tidy
go build
```

## **Create a BLS key**

```bash
bls_output_file=$HOME/.zrchain/sidecar/keys/bls.key.json
$HOME/zenrock-validators/utils/keygen/bls/bls --password $key_pass -output-file $bls_output_file
```

## **Build ecdsa binary**

```bash
cd $HOME/zenrock-validators/utils/keygen/ecdsa/
go mod tidy
go build
```

## **Create a ecdsa key**

```bash
ecdsa_output_file=$HOME/.zrchain/sidecar/keys/ecdsa.key.json
ecdsa_creation=$($HOME/zenrock-validators/utils/keygen/ecdsa/ecdsa --password $key_pass -output-file $ecdsa_output_file)
ecdsa_address=$(echo "$ecdsa_creation" | grep "Public address" | cut -d: -f2)
```

```bash
echo "ecdsa address: $ecdsa_address"
#Public address:  0xf36F077582B1c34D52f6e6964417fa52406827C1
```

**IMPORTANT** - to continue, you need to top up the generated ecdsa key with Ethereum Holesky test tokens You can use the faucet - [https://stakely.io/faucet/ethereum-holesky-testnet-eth](https://stakely.io/faucet/ethereum-holesky-testnet-eth)

{% hint style="info" %}
**IMPORTANT** - to continue you need to register on https://app.infura.io and get the following endpoints:

* mainnet eth: https://xxx
* holesky eth: https://xxx
* holesky eth: wss://xxx
{% endhint %}

**Set variables**

```bash
EIGEN_OPERATOR_CONFIG="$HOME/.zrchain/sidecar/eigen_operator_config.yaml"
TESTNET_HOLESKY_ENDPOINT="<HTTPS_TESTNET_HOLESKY_ENDPOINT>"
MAINNET_ENDPOINT="<HTTPS_MAINNET_ENDPOINT>"
OPERATOR_VALIDATOR_ADDRESS_TBD="<ADDR_zenrockVALOPER>"
OPERATOR_ADDRESS_TBU=$ecdsa_address
ETH_RPC_URL="<HTTPS_TESTNET_HOLESKY_ENDPOINT>"
ETH_WS_URL="<WSS_TESTNET_HOLESKY_ENDPOINT>"
ECDSA_KEY_PATH=$ecdsa_output_file
BLS_KEY_PATH=$bls_output_file
```

**Copy the original configuration files**

```bash
cp $HOME/zenrock-validators/configs/eigen_operator_config.yaml $HOME/.zrchain/sidecar/
cp $HOME/zenrock-validators/configs/config.yaml $HOME/.zrchain/sidecar/
```

**Change data in config.yaml**

```bash
sed -i "s|EIGEN_OPERATOR_CONFIG|$EIGEN_OPERATOR_CONFIG|g" "$HOME/.zrchain/sidecar/config.yaml"
sed -i "s|TESTNET_HOLESKY_ENDPOINT|$TESTNET_HOLESKY_ENDPOINT|g" "$HOME/.zrchain/sidecar/config.yaml"
sed -i "s|MAINNET_ENDPOINT|$MAINNET_ENDPOINT|g" "$HOME/.zrchain/sidecar/config.yaml"
```

**Change data in eigen\_operator\_config.yaml**

```bash
sed -i "s|OPERATOR_VALIDATOR_ADDRESS_TBD|$OPERATOR_VALIDATOR_ADDRESS_TBD|g" "$HOME/.zrchain/sidecar/eigen_operator_config.yaml"
sed -i "s|OPERATOR_ADDRESS_TBU|$OPERATOR_ADDRESS_TBU|g" "$HOME/.zrchain/sidecar/eigen_operator_config.yaml"
sed -i "s|ETH_RPC_URL|$ETH_RPC_URL|g" "$HOME/.zrchain/sidecar/eigen_operator_config.yaml"
sed -i "s|ETH_WS_URL|$ETH_WS_URL|g" "$HOME/.zrchain/sidecar/eigen_operator_config.yaml"
sed -i "s|ECDSA_KEY_PATH|$ECDSA_KEY_PATH|g" "$HOME/.zrchain/sidecar/eigen_operator_config.yaml"
sed -i "s|BLS_KEY_PATH|$BLS_KEY_PATH|g" "$HOME/.zrchain/sidecar/eigen_operator_config.yaml"
```

**Create a service file**

```bash
tee /etc/systemd/system/zenrock-sidecar.service > /dev/null <<EOF
[Unit]
Description=Zenrock-sidecar
After=network-online.target

[Service]
User=$USER
ExecStart=$HOME/.zrchain/sidecar/bin/zenrock-sidecar
Restart=on-failure
RestartSec=30
LimitNOFILE=65535
Environment="OPERATOR_BLS_KEY_PASSWORD=$key_pass"
Environment="OPERATOR_ECDSA_KEY_PASSWORD=$key_pass"
Environment="SIDECAR_CONFIG_FILE=$HOME/.zrchain/sidecar/config.yaml"

[Install]
WantedBy=multi-user.target
EOF
```

```bash
systemctl daemon-reload
systemctl enable zenrock-sidecar
systemctl restart zenrock-sidecar && journalctl -u zenrock-sidecar -f -o cat
```

{% hint style="info" %}
**Don't forget to save the directory $HOME/.zrchain/sidecar/**
{% endhint %}
