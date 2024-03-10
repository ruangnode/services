# Page 1

### Manual Installation <a href="#installation" id="installation"></a>

Official DocumentationRecommended Hardware: 6 Cores, 8GB RAM, 400GB of storage (NVME)

```bash
# install dependencies, if needed
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

Node NameWalletPort

```bash
# install go, if needed
cd $HOME
VER="1.21.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin

# set vars
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export CROSSFI_CHAIN_ID="crossfi-evm-testnet-1"" >> $HOME/.bash_profile
echo "export CROSSFI_PORT="36"" >> $HOME/.bash_profile
source $HOME/.bash_profile

# download binary
cd $HOME
wget https://github.com/crossfichain/crossfi-node/releases/download/v0.3.0-prebuild3/crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz && tar -xf crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz
tar -xvf crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz
chmod +x $HOME/bin/crossfid
mv $HOME/bin/crossfid $HOME/go/bin
rm -rf crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz $HOME/bin

# config and init app
crossfid config node tcp://localhost:${CROSSFI_PORT}657
crossfid config keyring-backend os
crossfid config chain-id crossfi-evm-testnet-1
rm -rf testnet ~/.mineplex-chain
git clone https://github.com/crossfichain/testnet.git
mv $HOME/testnet/ $HOME/.mineplex-chain/

# download genesis and addrbook
wget -O $HOME/.mineplex-chain/config/genesis.json https://testnet-files.itrocket.net/crossfi/genesis.json
wget -O $HOME/.mineplex-chain/config/addrbook.json https://testnet-files.itrocket.net/crossfi/addrbook.json

# set seeds and peers
SEEDS="dd83e3c7c4e783f8a46dbb010ec8853135d29df0@crossfi-testnet-seed.itrocket.net:36656"
PEERS="66bdf53ec0c2ceeefd9a4c29d7f7926e136f114a@crossfi-testnet-peer.itrocket.net:36656,b88d969ba0e158da1b4066f5c17af9da68c52c7a@65.109.53.24:44656,94eac2bd4f373b31ee9897fd5a2ab4a05080390b@65.108.127.160:26656,dda09f9625cab3fb655c22ef85d756fc77132b9d@167.235.102.45:10956,01e0df1e6932c371640cf44e80c8f0fd28675a6b@65.109.93.58:26056,5ebd3b1590d7383c0bb6696ad364934d7f1c984e@160.202.128.199:56156,c8914e513463791d91cc9ab35035c0c1111f307f@84.247.183.225:36656,b0b01c08d7d4c6c2740cc5fe6ea74eb7fdde64f2@38.242.151.229:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.mineplex-chain/config/config.toml

# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${CROSSFI_PORT}317%g;
s%:8080%:${CROSSFI_PORT}080%g;
s%:9090%:${CROSSFI_PORT}090%g;
s%:9091%:${CROSSFI_PORT}091%g;
s%:8545%:${CROSSFI_PORT}545%g;
s%:8546%:${CROSSFI_PORT}546%g;
s%:6065%:${CROSSFI_PORT}065%g" $HOME/.mineplex-chain/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${CROSSFI_PORT}658%g;
s%:26657%:${CROSSFI_PORT}657%g;
s%:6060%:${CROSSFI_PORT}060%g;
s%:26656%:${CROSSFI_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${CROSSFI_PORT}656\"%;
s%:26660%:${CROSSFI_PORT}660%g" $HOME/.mineplex-chain/config/config.toml

# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.mineplex-chain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.mineplex-chain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.mineplex-chain/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "10000000000000mpx"|g' $HOME/.mineplex-chain/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.mineplex-chain/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.mineplex-chain/config/config.toml

# create service file
sudo tee /etc/systemd/system/crossfid.service > /dev/null <<EOF
[Unit]
Description=Crossfi node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.mineplex-chain
ExecStart=$(which crossfid) start --home $HOME/.mineplex-chain
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# reset and download snapshot
crossfid tendermint unsafe-reset-all --home $HOME/.mineplex-chain
if curl -s --head curl https://testnet-files.itrocket.net/crossfi/snap_crossfi.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://testnet-files.itrocket.net/crossfi/snap_crossfi.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mineplex-chain
    else
  echo no have snap
fi

# enable and start service
sudo systemctl daemon-reload
sudo systemctl enable crossfid
sudo systemctl restart crossfid && sudo journalctl -u crossfid -f
```

### Create wallet <a href="#create-wallet" id="create-wallet"></a>

```bash
# to create a new wallet, use the following command. don’t forget to save the mnemonic
crossfid keys add $WALLET

# to restore exexuting wallet, use the following command
crossfid keys add $WALLET --recover

# save wallet and validator address
WALLET_ADDRESS=$(crossfid keys show $WALLET -a)
VALOPER_ADDRESS=$(crossfid keys show $WALLET --bech val -a)
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile

# check sync status, once your node is fully synced, the output from above will print "false"
crossfid status 2>&1 | jq .SyncInfo

# before creating a validator, you need to fund your wallet and check balance
crossfid query bank balances $WALLET_ADDRESS
```

### Create validator <a href="#create-validator" id="create-validator"></a>

MonikerIdentityDetailsAmount, mpxCommission rateCommission max rateCommission max change rate

```bash
crossfid tx staking create-validator \
--amount 1000000mpx \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(crossfid tendermint show-validator) \
--moniker "test" \
--identity "" \
--details "I love blockchain ❤️" \
--chain-id crossfi-evm-testnet-1 \
--gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx \
-y
```

### Monitoring <a href="#monitoring" id="monitoring"></a>

If you want to have set up a monitoring and alert system use our cosmos nodes [monitoring guide with uptime kuma](https://docs.ruangnode.com/monitoring-and-alerting/monitoring-stack)

### Security <a href="#security" id="security"></a>

To protect you keys please don\`t share your privkey, mnemonic and follow a basic security rules

#### Set up ssh keys for authentication <a href="#ssh" id="ssh"></a>

You can use this [guide](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04) to configure ssh authentication and disable password authentication on your server

#### Firewall security <a href="#firewall" id="firewall"></a>

Set the default to allow outgoing connections, deny all incoming, allow ssh and node p2p port

```bash
sudo ufw default allow outgoing 
sudo ufw default deny incoming 
sudo ufw allow ssh/tcp 
sudo ufw allow ${CROSSFI_PORT}656/tcp
sudo ufw enable
```

### Delete node <a href="#delete" id="delete"></a>

```bash
sudo systemctl stop crossfid
sudo systemctl disable crossfid
sudo rm -rf /etc/systemd/system/crossfid.service
sudo rm $(which crossfid)
sudo rm -rf $HOME/.mineplex-chain
sed -i "/CROSSFI_/d" $HOME/.bash_profile
```
