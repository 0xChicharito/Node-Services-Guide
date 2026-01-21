---
description: Minimum hardware requirement 2 Cores, 2G Ram, min 40G SSD, Ubuntu 22.04.
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
---

# 🔘 Dill Chain

Twitter: [https://x.com/dill\_xyz](https://x.com/dill_xyz)\_

Discord: [https://discord.com/invite/dill](https://discord.com/invite/dill)

Explorer: [https://andes.dill.xyz/](https://andes.dill.xyz/)

Guide Docs: [https://past-chokeberry-e85.notion.site/andes-doc-a23a9f71a85a4be9a6737e54d2674060](https://past-chokeberry-e85.notion.site/andes-doc-a23a9f71a85a4be9a6737e54d2674060)

### 1. Server preparation

```
sudo apt update && apt upgrade -y
sudo apt install wget curl make clang net-tools pkg-config libssl-dev build-essential jq lz4 gcc unzip snapd -y
```

### 2. Install Node

#### 2.1 Download & Extract the package

```
cd $HOME && curl -O https://dill-release.s3.ap-southeast-1.amazonaws.com/linux/dill.tar.gz && \
tar -xzvf dill.tar.gz && cd dill
```

#### 2.2 Generate Validator Keys

```
./dill_validators_gen new-mnemonic --num_validators=1 --chain=andes --folder=./
```

`sample output`

```
ubuntu@ip-xxxx:~/dill$ ./dill_validators_gen new-mnemonic --num_validators=1 --chain=andes --folder=./

***Using the tool on an offline and secure device is highly recommended to keep your mnemonic safe.***

Please choose your language ['1. العربية', '2. ελληνικά', '3. English', '4. Français', '5. Bahasa melayu', '6. Italiano', '7. 日本語', '8. 한국어', '9. Português do Brasil', '10. român', '11. Türkçe', '12. 简体中文']:  [English]: 3
Please choose the language of the mnemonic word list ['1. 简体中文', '2. 繁體中文', '3. čeština', '4. English', '5. Italiano', '6. 한국어', '7. Português', '8. Español']:  [english]: 4
Create a password that secures your validator keystore(s). You will need to re-enter this to decrypt them when you setup your Dill validators.:
Repeat your keystore password for confirmation:
The amount of DILL token to be deposited(2500 by default). [2500]:
This is your mnemonic (seed phrase). Write it down and store it safely. It is the ONLY way to retrieve your deposit.


Creating your keys.
Creating your keystores:	  [####################################]  1/1
Verifying your keystores:	  [####################################]  1/1
Verifying your deposits:	  [####################################]  1/1

Success!
Your keys can be found at: ./validator_keys


Press any key.
ubuntu@ip-xxxx:~/dill$
```

This will generate the validator keys and save them in ./validator\_keys directory

```
ls -ltr ./validator_keys
```

#### 2.3 Import your keys to your keystore

```
./dill-node accounts import --andes --wallet-dir ./keystore --keys-dir validator_keys/ --accept-terms-of-use
```

#### 2.4 Write the password you configured in the previous step into a file:

```
echo nodesync@123 > walletPw.txt
```

`nodesync@123` is your password, You can change it.

#### 2.5 Start the light validator node

```
./start_light.sh -p walletPw.txt
```

`sample output`

```
ubuntu@xxxxx:~/dill$ ./start_light.sh -p walletPw.txt
Option --pwdfile, argument 'walletPw.txt'
Remaining arguments:
using password file at walletPw.txt
start light node
start light node done
ubuntu@xxxxx:~/dill$ nohup: redirecting stderr to stdout
```

#### 2.6 Check health node

```
./health_check.sh -v
```

### 3. Staking

#### 3.1 Faucet

Get faucet into your EVM wallet from the Andes channel. Your wallet form before on galxe.

Use WinSCP or Terminus, Then go to folder `/dill/validator_keys` directory and get your `deposit_data-xxxx.json` file and upload it to the site bellow.

#### 3.2 Visit [https://staking.dill.xyz/](https://staking.dill.xyz/)

Upload deposit\_data-\*.json

Connect to MetaMask，make sure you have enough balance（>2500 DILL)

Send deposit，using MetaMask to send a deposit transaction

### 4. Services Management

```
cd $HOME/dill
```

#### 4.1 Check health node

```
./health_check.sh -v
```

#### 4.2 Stop node

```
ps -ef | grep dill-node | grep -v grep | awk '{print $2}' | xargs kill
```

#### 4.3 Start node

```
./start_light.sh -p walletPw.txt
```

#### 4.4 Check logs

```
tail -f -n 100 $HOME/dill/light_node/logs/dill.log
```

#### 4.5 Check on Explorer

Copy your pubkey in file deposit\_data-xxxx.json and search it on explorer: [https://andes.dill.xyz/validators](https://andes.dill.xyz/validators)

### 5. Fix port 8080 (change to 8081)

#### 5.1 Stop node

```
cd $HOME/dill
```

```
ps -ef | grep dill-node | grep -v grep | awk '{print $2}' | xargs kill
```

#### 5.1 Update file (use port 8081)

```
rm -rf $HOME/dill/health_check.sh && rm -rf $HOME/dill/start_light.sh && \
wget -O  $HOME/dill/start_light.sh https://raw.githubusercontent.com/nodesynctop/Dill/main/start_light.sh && \
wget -O  $HOME/dill/health_check.sh https://raw.githubusercontent.com/nodesynctop/Dill/main/health_check.sh && \
chmod +x health_check.sh && chmod +x start_light.sh
```

#### 5.2 Start node

```
./start_light.sh -p walletPw.txt
```
