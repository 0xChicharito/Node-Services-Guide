# 🔗 Upgrade

### 1. Update `config.yaml` <a href="#id-1.-stop-node" id="id-1.-stop-node"></a>

<pre class="language-bash"><code class="lang-bash"><strong>nano /root/.zrchain/sidecar/config.yaml
</strong></code></pre>

```bash
grpc_port: 9191
state_file: "cache.json"
operator_config: "/root/.zrchain/sidecar/eigen_operator_config.yaml"
network: "testnet"
eth_oracle:
  rpc:
    local: "http://127.0.0.1:8545"
    testnet: "TESTNET_HOLESKY_ENDPOINT"
    mainnet: "MAINNET_ENDPOINT"
  contract_addrs:
    service_manager: "0x3AD648DfE0a6D80745ab2Ec97CB67c56bfBEc032"
    price_feed: "0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419"
    network_name: "Holešky Ethereum Testnet"
solana_rpc:
  testnet: "https://api.testnet.solana.com"
  mainnet: ""
proxy_rpc:
  url: ""
  user: ""
  password: ""
neutrino:
# root path to your setup folder, where validator/sidecar configuration files/data resides
  path: "/root/.zrchain/sidecar/neutrino"
```

### 2. Update `eigen_operator_config.yaml` <a href="#id-1.-stop-node" id="id-1.-stop-node"></a>

```bash
nano /root/.zrchain/sidecar/config.yaml
```

```bash
register_operator_on_startup: true
register_on_startup: true
production: true
#To be manually updated
operator_address: OPERATOR_ADDRESS
operator_validator_address: OPERATOR_VALIDATOR_ADDRESS

# EigenLayer Slasher contract address
# This is the address of the contracts which are deployed in the anvil saved state
# The saved eigenlayer state is located in tests/anvil/credible_squaring_avs_deployment_output.json
avs_registry_coordinator_address: 0xFbB0cbF0d14C8BaE1f36Cd4Dff792ca412b72Af0
operator_state_retriever_address: 0xe7FDe0EFCECBbcC25F326EdC80E6B79c1482dAaB

# ETH RPC URL
eth_rpc_url: ETH_RPC_URL
eth_ws_url: ETH_WS_URL
# ECDSA key
ecdsa_private_key_store_path: ECDSA_KEY_PATH

# We are using bn254 curve for bls keys
bls_private_key_store_path: BLS_KEY_PATH

# address which the aggregator listens on for operator signed messages
aggregator_server_ip_port_address: avs-aggregator.gardia.zenrocklabs.io:8090

# avs node spec compliance https://eigen.nethermind.io/docs/spec/intro
eigen_metrics_ip_port_address: 0.0.0.0:9292
enable_metrics: true
metrics_address: 0.0.0.0:9292
node_api_ip_port_address: 0.0.0.0:9191
enable_node_api: true


# address of token to deposit tokens into when registering on startup
token_strategy_addr: 0x80528D6e9A2BAbFc766965E0E26d5aB08D9CFaF9
service_manager_address: 0x3AD648DfE0a6D80745ab2Ec97CB67c56bfBEc032
zr_chain_rpc_address: localhost:9790
```

### 3. Download binary  <a href="#id-1.-stop-node" id="id-1.-stop-node"></a>

```bash
wget -O $HOME/.zrchain/sidecar/bin/validator_sidecar https://github.com/zenrocklabs/zrchain/releases/download/v5.3.4/validator_sidecar
chmod +x $HOME/.zrchain/sidecar/bin/validator_sidecar
```

### 4. Stop node and update service <a href="#id-1.-stop-node" id="id-1.-stop-node"></a>

```bash
systemctl stop zenrock-sidecar
```

```bash
tee /etc/systemd/system/zenrock-sidecar.service > /dev/null <<EOF
[Unit]
Description=Zenrock-sidecar
After=network-online.target

[Service]
User=$USER
ExecStart=$HOME/.zrchain/sidecar/bin/validator_sidecar
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

<figure><img src="../../../.gitbook/assets/Screenshot 2024-11-19 232102 (1).png" alt=""><figcaption></figcaption></figure>
