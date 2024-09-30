# Installation By Dock (Recommend)

```autohotkey
sudo apt update && sudo apt upgrade -y
```

```autohotkey
sudo apt install docker-compose
```

```autohotkey
sudo apt-get update
sudo apt-get install -y make gcc
```

Install GO v.1.22.4 (Stable)

```autohotkey
cd $HOME
version="1.22.4"
wget "https://golang.org/dl/go$version.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$version.linux-amd64.tar.gz"
rm "go$version.linux-amd64.tar.gz"
export PATH=$PATH:/usr/local/go/bin
go version
```

Check and change new version at here  "v0.0.7"

{% embed url="https://github.com/allora-network/allora-chain/releases" %}

```autohotkey
curl -sSL https://raw.githubusercontent.com/allora-network/allora-chain/main/install.sh | bash -s -- v0.0.7
```

Change \<latest-release-tag> same above version

```
git clone -b <latest-release-tag> https://github.com/allora-network/allora-chain.git
cd allora-chain && make all
```

Change \<latest-release-tag> same above version

```autohotkey
git switch -c v0.2.5-dev

Make install

sudo docker-compose pull
```

\-d chạy ẩn

```autohotkey
sudo docker-compose up -d
```

Check logs

```
sudo docker-compose logs -f
```

**Useful Commands**

<pre class="language-autohotkey"><code class="lang-autohotkey">//Exact wallet 

sudo cat validator0.account_info

//keys list

<strong>allorad keys list --home="$APP_HOME" --keyring-backend=test
</strong>
//Recover keys

allorad keys add &#x3C;your wallet> --recover --home="$APP_HOME" --keyring-backend=test

//Create new key

allorad keys add &#x3C;your wallet> --home="$APP_HOME" --keyring-backend=test

//Edit validator

<strong>Note: Important command before using another command
</strong>
<a data-footnote-ref href="#user-content-fn-1">sudo docker-compose exec validator0 bash</a>

 **Once you're in you'll see the root@xxxxx changed from your normal environment Check your wallet 

allorad --home="$APP_HOME" tx staking edit-validator \
  --new-moniker "&#x3C;your wallet>" \
  --website "https://testnet.zkstore.in" \
  --identity "" \
  --from "validator0" \
  --keyring-backend=test \
  --chain-id ${NETWORK} \
  --gas auto \
  --gas-adjustment 1.5 \
  --gas-prices 1uallo \
  -y

//Check balance

allorad q bank balances $(allorad --home="$APP_HOME" keys show validator0 -a)

//Check your wallet 

allorad --home=$APP_HOME keys --keyring-backend=test list

//Check your validator address 

VAL_PUBKEY=$(allorad --home=$APP_HOME comet show-validator | jq -r .key)allorad --home=$APP_HOME q staking validators -o=json | \    jq '.validators[] | select(.consensus_pubkey.value=="'$VAL_PUBKEY'")'
 
//Check your wallet balance 

allorad --home=$APP_HOME q bank balances YOUR_WALLET_ADDRESS --node tcp://localhost:26657 

//Delegate 

allorad tx staking delegate YOUR_VALIDATOR_ADDRESS 30000000000uallo \    --chain-id=edgenet \    --home=$APP_HOME \    --keyring-backend=test \    --from=YOUR_WALLET_NAME --node tcp://localhost:26657

//Check status 

allorad status 2>&#x26;1 | jq

curl -s http://localhost:26658/status | jq .

curl -s http://localhost:26658/status | jq .result.sync_info.catching_up

</code></pre>



[^1]: 
