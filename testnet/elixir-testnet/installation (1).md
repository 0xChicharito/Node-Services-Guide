# ⚙️ Installation

## Docker Installation

**Install Git**

```bash
sudo apt update
sudo apt install git
git --version
```

**Install Docker**

```bash
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common -y
curl -fsSL 
https://download.docker.com/linux/ubuntu/gpg
 | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] 
https://download.docker.com/linux/ubuntu
 $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce jq python3-pip -y
```

```bash
docker --version
```

## Mint MOCK Elixir Tokens On Sepolia

Use Wallet of Metamask for mintting Sepolia ETH

{% embed url="https://cloud.google.com/application/web3/u/0/faucet/ethereum/sepolia" %}

Verify you are connected to the Ethereum Sepolia network and then click the "MINT 1,000 MOCK" button

{% embed url="https://testnet-3.elixir.xyz/" %}

Stake Your MOCK Tokens

## **Download The Environment Template**

```bash
sudo mkdir -p /root/elixir
```

Download below template and put into above folder

{% embed url="https://files.elixir.finance/validator.env" %}

```bash
STRATEGY_EXECUTOR_IP_ADDRESS= <IP VPS>
STRATEGY_EXECUTOR_DISPLAY_NAME= <Name>
STRATEGY_EXECUTOR_BENEFICIARY= <Wallet address>
SIGNER_PRIVATE_KEY= <Private key>
```

## Running Your Validator <a href="#running-your-validator" id="running-your-validator"></a>

#### **Pull The Docker Image** <a href="#pull-the-docker-image" id="pull-the-docker-image"></a>

```bash
docker pull elixirprotocol/validator:v3
```

**Start Your Validator**

```bash
docker run -d \
  --env-file /root/elixir/validator.env \
  --name elixir \
  --restart unless-stopped \
  elixirprotocol/validator:v3
```

**Check logs**

```bash
docker logs -f elixir
```

## Upgrading your validator

```bash
docker kill elixir
docker rm elixir
docker pull elixirprotocol/validator:v3
```

