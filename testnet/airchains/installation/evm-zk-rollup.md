# 🔺 EVM ZK Rollup

### **1. Clone GitHub Repositories**

```
git clone https://github.com/airchains-network/evm-station.git
git clone https://github.com/airchains-network/tracks.git
```

### 2. Setting Up and Running the EVM Station: <a href="#id-2.-setting-up-and-running-the-evm-station" id="id-2.-setting-up-and-running-the-evm-station"></a>

```
cd evm-station
```

```
go mod tidy
```

```
/bin/bash ./scripts/local-setup.sh
/bin/bash ./scripts/local-start.sh 
```

#### **Get Your Private Key of EVM Station** <a href="#get-your-private-key-of-evm-station" id="get-your-private-key-of-evm-station"></a>

```
/bin/bash ./scripts/local-keys.sh
```

Result:

```
⚡ root@vmi1797354  ~/evm-station   master ±  /bin/bash ./scripts/local-keys.sh 
45130F31XXXXXXXXXXA3E1C9D5C14359459A87E8B10638E86F502922F3811D24
```

_Save private key to import MetaMask Wallet_

### 3. Setting Up DA Keys and Running DA Client: (Eigen) <a href="#id-3.-setting-up-da-keys-and-running-da-client-eigen" id="id-3.-setting-up-da-keys-and-running-da-client-eigen"></a>

```
wget https://github.com/airchains-network/tracks/releases/download/v0.0.2/eigenlayer
```

Create and List Keys:

```
./eigenlayer operator keys create --key-type ecdsa [keyname]
```

Result:

```
eigenlayer operator keys create --key-type ecdsa test
? Enter password to encrypt the ecdsa private key:
ECDSA Private Key (Hex):  b3eba201405d5b5f7aaa9adf6bb734dc6c0f448ef64dd39df80ca2d92fca6d7b
Please backup the above private key hex in safe place.

Key location: /home/ubuntu/.eigenlayer/operator_keys/test.ecdsa.key.json
Public Key hex:  f87ee475109c2943038b3c006b8a004ee17bebf3357d10d8f63ef202c5c28723906533dccfda5d76c1da0a9f05cc6d32085ca1af8aaab5a28171474b1ad0aa68
Ethereum Address 0x6a8c0D554a694899041E52a91B4EC3Ff23d8aBD5
```

_Remember this PUB\_KEY_

### 4. Setting Up and Running Tracks: <a href="#id-4.-setting-up-and-running-tracks" id="id-4.-setting-up-and-running-tracks"></a>

Open new tmux tab

```
sudo rm -rf ~/.tracks
```

```
cd tracks
```

```
go mod tidy
```

#### **Initiate Sequencer:** <a href="#initiate-sequencer" id="initiate-sequencer"></a>

```
go run cmd/main.go init --daRpc "da-rpc" --daKey "daKey" --daType "<da-type>" --moniker "<moniker-name>" --stationRpc "http://127.0.0.1:8545" --stationAPI "http://127.0.0.1:8545" --stationType "evm"
```

* \--daRpc: "**disperser-holesky.eigenda.xyz"**
* \--daKey: \<PUB KEY of DA key>
* \--daType: "eigen"
* \--moniker: "moniker name"

Sample:

```
go run cmd/main.go init --daRpc "disperser-holesky.eigenda.xyz" --daKey "6a822c60ca15c81e824df6c2ff83f050224473bcbaabccab2d8ac084a7749fbb745c3ae96c0b3964083409c39d074cb6000ddb8b093ca3e98bde7c78f794267e" --daType "eigen" --moniker "JosephTran" --stationRpc "http://0.0.0.0:8545" --stationAPI "http://0.0.0.0:8545" --stationType "evm"
```

#### **Create Keys for Junction:** <a href="#create-keys-for-junction" id="create-keys-for-junction"></a>

Generate keys for the Junction component using the following command:

```
go run cmd/main.go keys junction --accountName <account-name> --accountPath $HOME/.tracks/junction-accounts/keys
```

Replace `<account-name>`

Sample:

```
go run cmd/main.go keys junction --accountName josephtran --accountPath $HOME/.tracks/junction-accounts/keys
```

Result:

```
[2024-06-27 08:04:19] » Account created: josephtran
[2024-06-27 08:04:19] » Mnemonic: (save your seed phrase)
[2024-06-27 08:04:19] » Address: air124y5xaxaxjj3mfj5ek7kuv2l3347ylumt9jhr9
[2024-06-27 08:04:19] » Please save this mnemonic key for account recovery
[2024-06-27 08:04:19] » Please save this address for future reference
```

Faucet token AMF for this wallet: `air124y5xaxaxjj3mfj5ek7kuv2l3347ylumt9jhr9`

#### **Initiate Prover:** <a href="#initiate-prover" id="initiate-prover"></a>

```
go run cmd/main.go prover v1EVM
```

Result:

```
08:05:55 INF compiling circuit08:05:55
INF parsed circuit inputs nbPublic=150 nbSecret=008:05:56 INF building constraint builder nbConstraints=47975
[2024-06-27 08:06:43] » Proving key and Verification key generated and saved successfully
```

#### **Create Station on Junction:** <a href="#create-station-on-junction" id="create-station-on-junction"></a>

```
go run cmd/main.go create-station --accountName <account-name> --accountPath $HOME/.tracks/junction-accounts/keys --jsonRPC "https://junction-testnet-rpc.synergynodes.com/" --info "EVM Track" --tracks <wallet-address> --bootstrapNode "/ip4/192.168.1.24/tcp/2300/p2p/<node_id>"
```

Sample:

```
go run cmd/main.go create-station --accountName josephtran02 --accountPath $HOME/.tracks/junction-accounts/keys --jsonRPC "https://junction-testnet-rpc.synergynodes.com/" --info "EVM Track" --tracks air15s968ffwy7xykj83fmyhsm47zhptm3kj5l4949 --bootstrapNode "/ip4/53.213.110.159/tcp/2300/p2p/12D3KooWDaaoJcxxxxxxxqZCockmeHyy3QgaKdG5oPwGQNM5X"
```

* \--account-name `josephtran`
* \--jsonRPC `https://junction-testnet-rpc.synergynodes.com/`
* \--track `air124y5xaxaxjj3mfj5ek7kuv2l3347ylumt9jhr9`
* \--bootstrapNode: /ip4/`<IP_VPS>`/tcp/2300/p2p/`<nodeID>`

You can find Node ID at: `sequencer.toml`

```
nano /root/.tracks/config/sequencer.toml
```

Result

```
[2024-06-27 08:35:49] » tracks address: air124y5xaxaxjj3mfj5ek7kuv2l3347ylumt9jhr9
accountAddress: air124y5xaxaxjj3mfj5ek7kuv2l3347ylumt9jhr9
[2024-06-27 08:35:49] » Currently user have 1.000000AMF
[2024-06-27 08:36:03] » tx hash: A68BC3CDD523BA300B9474CDA9723B553FC96CF7EB682A77260481A43BAB9DD1
[2024-06-27 08:36:03] » /root/.tracks/config/genesis.json created
[2024-06-27 08:36:03] » vrfPrivKey ID: d929534a15xxxxxxxxxxxxxxxxxxxe67402a5a8ad57542e97b0d
[2024-06-27 08:36:03] » vrfPubKey ID: 86767xxxxxxxxxxxxxxxxxxxxxxx40228c6fb5043a921414
[2024-06-27 08:36:03] » Successfully Created VRF public and private Keys
[2024-06-27 08:36:03] » Successfully created station
```

#### **Start Node:** <a href="#start-node" id="start-node"></a>

```
go run cmd/main.go start
```

![](https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FQAw6ZgFvzZ1iKwUjuqmd%252FScreen%2520Shot%25202024-06-27%2520at%252021.51.22.png%3Falt%3Dmedia%26token%3D5f7f566d-2a58-4990-a2aa-d91088f841e3\&width=768\&dpr=4\&quality=100\&sign=95b8e00c\&sv=1)

#### Add Wallet and your network to Meta mask: <a href="#add-wallet-and-your-network-to-meta-mask" id="add-wallet-and-your-network-to-meta-mask"></a>

Use private key which created by `/bin/bash ./scripts/local-keys.sh`

#### Add Network: <a href="#add-network" id="add-network"></a>

Open EVM RPC port to connect from Client:

```
nano /root/.evmosd/config/app.toml
```

```
###############################################################################
###                           JSON RPC Configuration                        ###
###############################################################################

[json-rpc]

# Enable defines if the gRPC server should be enabled.
enable = true

# Address defines the EVM RPC HTTP server address to bind to.
address = "0.0.0.0:8545"
```

Restart EVM Station:

```
/bin/bash ./scripts/local-start.sh 
```

Add wallet from Client:

\*Chain ID will auto get after input URL network:

Choose Network name & Token Symbol

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FbvzSuRMxFO2QRf0QyoYv%252FScreen%2520Shot%25202024-06-27%2520at%252021.20.43.png%3Falt%3Dmedia%26token%3D77772490-8746-42d4-a424-7f0f9d81f564&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=92235d02&#x26;sv=1" alt=""><figcaption></figcaption></figure>

You can see your coin:

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FpOxsrAQvpz2Qtfxv1p0K%252FScreen%2520Shot%25202024-06-27%2520at%252021.25.58.png%3Falt%3Dmedia%26token%3D3c549188-d786-4672-9584-69d9aec0bbdd&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=aa5981ca&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Source: [https://service.josephtran.xyz/testnet/airchains/evm-zk-rollup](https://service.josephtran.xyz/testnet/airchains/evm-zk-rollup)
