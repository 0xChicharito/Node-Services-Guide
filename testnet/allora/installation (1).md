# Installation

#### Hardware requirements The following requirements are recommended for running ALLORA: <a href="#hardware-requirements-the-following-requirements-are-recommended-for-running-allora" id="hardware-requirements-the-following-requirements-are-recommended-for-running-allora"></a>

* **6 or more physical CPU cores**
* **At least 1TB of SSD disk storage**
* **At least 16GB of memory (RAM)**
* **At least 500mbps network bandwidth**

```autohotkey
# Update & install dependencies
sudo apt update && sudo apt upgrade -y
sudo apt install clang pkg-config libssl-dev curl git wget htop tmux build-essential jq make lz4 gcc unzip -y


# Install Go
cd $HOME
VER="1.22.2"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
go version


# Set Vars
MONIKER=<YOUR_MONIKER_NAME_GOES_HERE>
echo "export MONIKER=$MONIKER" >> $HOME/.bash_profile
echo "export ALLORA_CHAIN_ID="testnet"" >> $HOME/.bash_profile
echo "export ALLORA_PORT="40"" >> $HOME/.bash_profile
source $HOME/.bash_profile


# Download Binary
cd $HOME
rm -rf allora
git clone https://github.com/allora-network/allora-chain allora
cd allora
git checkout v0.0.10
make install


# Config and Init App
allorad init $MONIKER --chain-id $ALLORA_CHAIN_ID


# Add Genesis File and Addrbook
wget -O $HOME/.allorad/config/genesis.json https://testnet-files.syanodes.my.id/allora-genesis.json
wget -O $HOME/.allorad/config/addrbook.json https://testnet-files.syanodes.my.id/allora-addrbook.json


#Configure Seeds and Peers
seed=""
peers="daddf466ffda9f06406c3951fea37148e0cc4254@188.40.66.173:26756,ad5a818a2fce6c96bf040c11731e51d253d6ae12@34.125.70.129:26656,18794b770e44eb0fa6cd8509cb91ba5e35717627@206.125.34.196:26662,0e7874473303951f172b67d7fcadab2416d00dad@91.242.214.227:26656,c909c9303c070dd9ced28bafd90a04b1d9179768@89.110.91.116:26656,6b0bae238972c087bfff9f95de243735673ea963@77.64.253.85:26656,5e0cf8c74e9c21d020f7b494d3eb8aef42019346@184.174.33.241:26656,549364a232796942118b5be5f8d62005895eacd7@148.113.16.49:26656,ec462be83cb8870cd79c2fda8677cec45f245949@207.65.221.52:26656,c77c37ff27a9af4924a8839cedd3b22dc21e388f@84.64.112.126:26656,16541ace744a8e58a942bdbe2ddff3a16c2dce90@136.243.78.225:26656,3584b56ad639bb46c20ec9e5d05f39b630c19c4b@54.81.195.244:32001,ea8420deaa561f9eabe620a122378d47c059939c@173.234.17.193:26656,c9d49bf9fdcfd9d304cebea81a4c2a2bc20f68b9@84.247.181.92:26656,c29fd18c07b78b520faeadf67459ab0114402be0@185.209.29.83:26656,455215a20c14eb4801c34c7350ba0a7b040a5e82@38.45.66.178:26656,0e6578d28c31f5876409088ed51d2d3b5540d4ce@66.70.177.125:26656,1dda15c1b0e66f8e5c04344b2a639f51179de9f5@51.89.67.94:21656,2565f8b80bee76b4e26c61b8c5df70d33783bbd0@18.236.127.219:26500,a18a9be73bd26d8d97b30ee74173e1699a8bda6f@136.243.1.83:29656,e005548fecf92ae76d2bf9ebc4a0db863040aea5@113.165.23.45:26656,a2919076678b89c955477d8460c9c932bd9786e7@34.224.166.207:32010,e9d7579905d464d2691b7895012edf5c8279e351@168.119.31.198:56286,b4a3defc0aa044993711109028771da76f86d8d3@89.110.88.106:26656,4750426ca3e0c479bef9d2a639db03b17915ae34@84.247.167.216:26656,c614510aa8e6e20a254bc9aaed1a7078c48e888b@136.38.55.33:26656,9d83bd2586674ed5717c95d2935e3ff8f62d039f@45.94.209.4:26656,540da856351af2689c1413218b54e8ef2bfe63ab@185.211.6.114:26656,0e8ebf8b222c3650e1e48d748d1e160b14171673@89.116.28.226:46656,d1c5b2d12ac4ca49a342f797688ff459dd301702@163.172.68.250:26656,c47af4c7e8eadb7e00fb50d1c0a7bd894dcb67df@185.185.82.170:26656,d223b29165debcac10cc20a00848eadd77090860@80.92.214.29:26656,f784d1678dedc2204ba3e1a4ab04a39d2ada36a7@44.201.28.205:32011,d42629dfc1ba7733f782f9e6534c293b1c8114f7@149.28.152.142:26656,420c2386e132a056613f32b15c6596fd66e4d58f@65.108.225.207:46656,5f92a5e98b1a676a7c8774e1bb13e9aa728442f8@68.7.226.221:26656,97e5ee864b581a432cd742cd507236062fe2c105@203.113.174.180:27656,908ab062e33c1e291c3fd253a67ed5a19fb29087@62.72.5.161:26656,7274bbed56bb07987c2c7e10e610563609965757@84.203.117.234:26656,e0b1fbddbf9254aa89540f4409c016ea12c5b451@51.81.109.67:26656,2ea86e31cf10723ca97966f22efbcc9bbb36d455@34.86.171.35:26656,aa5c421ad84a78bdae42aa93c7a4530c0da2da26@31.220.72.81:26656,15e23295c829cbfb50e876b294c014c20bef53bd@54.196.52.114:32012,ca74bfeeec12f7879963267e5b758a05204d0f97@80.64.208.238:26656,2150d3c0095845ca11bea8a34c0d3ac8c4634e04@110.171.123.186:26656,5dea51d09e84bf84cf33f029a61f66be5b4f7e0a@81.31.244.52:26656,10bfcd1c24220544da433110c1f74f6c04e226a9@149.50.105.169:26656,b8598fc2037b2a3c3b6283be4ab8fa697fd0fd3d@212.126.35.133:26664,61807b914f99020cef984bbb16cdb3cec2a640cb@185.218.126.157:26656,19d11f8b63d120a66c754dd2f9c604b3fed0cfca@212.126.35.132:26656,86ed8efc38b145e256729ca3519ce9294e688a4d@38.242.138.142:26656,223e15952763cdc964d2f90dc5b35586f4fb38b5@89.110.89.214:26656,5664a8e0db4760b0c7414393eb443bf44787544a@37.27.124.38:26654,4b61584be380ceb18fc38a2e40db9de5ee1d19b7@51.15.15.233:26656,a066e59c46ae9f9c50f8e9752e4d3527161af780@87.246.108.81:26656,99c10c7c5ccad59090e34d0fd181dc9b779f1fc5@44.213.89.206:32002,501bf05da57dc7243878769027c4cc07d4b4c131@16.63.229.79:26656,dc4ba719971ec6c6ab1c1a26494569fe21bf7625@94.61.200.48:26656,462e675442969fe73ef8160c32677053670096ab@108.209.102.187:26643,f7b402b0969bf7328405e40a57d650f1ac20795b@108.209.99.65:26670,5425949026e175cf35e090617861e89a0904a38b@45.159.231.59:26656,1cf59abcc0c6da0be2bf759f44d2346172eda6c2@35.90.33.133:26656,316f0ca3746c959701d0d6165ed3e79aaff68cc1@195.201.245.178:56286"
sed -i.bak -e "s/^seed *=.*/seed = \"$seed\"/" ~/.allorad/config/config.toml
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.allorad/config/config.toml
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0uallo\"/" $HOME/.allorad/config/app.toml
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.allorad/config/config.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.allorad/config/config.toml


# Config Pruning
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="19"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.allorad/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.allorad/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.allorad/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.allorad/config/app.toml


# Set Custom Port
sed -i.bak -e "s%:1317%:${ALLORA_PORT}317%g;
s%:8080%:${ALLORA_PORT}080%g;
s%:9090%:${ALLORA_PORT}090%g;
s%:9091%:${ALLORA_PORT}091%g;
s%:8545%:${ALLORA_PORT}545%g;
s%:8546%:${ALLORA_PORT}546%g;
s%:6065%:${ALLORA_PORT}065%g" $HOME/.allorad/config/app.toml
sed -i.bak -e "s%:26658%:${ALLORA_PORT}658%g;
s%:26657%:${ALLORA_PORT}657%g;
s%:6060%:${ALLORA_PORT}060%g;
s%:26656%:${ALLORA_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${ALLORA_PORT}656\"%;
s%:26660%:${ALLORA_PORT}660%g" $HOME/.allorad/config/config.toml
sed -i \
  -e 's|^chain-id *=.*|chain-id = "testnet"|' \
  -e 's|^keyring-backend *=.*|keyring-backend = "test"|' \
  -e 's|^node *=.*|node = "tcp://localhost:40657"|' \
  $HOME/.allorad/config/client.toml


# Set Service File
sudo tee /etc/systemd/system/allorad.service > /dev/null <<EOF
[Unit]
Description=allora-testnet
After=network-online.target

[Service]
User=$USER
ExecStart=$(which allorad) start --home $HOME/.allorad
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF


# Enable and Start Service
sudo systemctl daemon-reload
sudo systemctl enable allorad
sudo systemctl start allorad 
sudo journalctl -fu allorad -o cat
```

