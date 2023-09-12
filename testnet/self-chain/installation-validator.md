# Installation Validator

| Chain ID   | Latest Version Tag	 | Custom Port |
| ---------- | ------------------- | ----------- |
| self-dev-1 | 0.2.2               | 113         |

Update and install packages for compiling

```
sudo apt update
sudo apt install curl git jq lz4 build-essential -y
```

#### User Management

{% hint style="info" %}
**Running in user** (Assume) : _admin_ We never used this username in our production !
{% endhint %}

Login as User _admin_

```bash
su - admin
```

or

```bash
sudo su - admin
```

> <mark style="color:blue;background-color:blue;">Replace</mark> <mark style="color:blue;background-color:blue;"></mark><mark style="color:blue;background-color:blue;">**YOUR\_MONIKER\_GOES\_HERE**</mark> <mark style="color:blue;background-color:blue;"></mark><mark style="color:blue;background-color:blue;">with your validator name</mark>

```
MONIKER="YOUR_MONIKER_GOES_HERE"
```

```bash
pwd

/home/admin
```

{% hint style="info" %}
**Running in user** : admin
{% endhint %}

### Install Golang

```bash
# Install Go 1.20.1
ver="1.20"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version

# Download and install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```

#### Install Cosmovisor and service

```bash
# Download and install binary
cd $HOME
curl -o selfchaind https://1250888074-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FcIZFCZY4EPKDYaPcDZLG%2Fuploads%2FMuuuXZOR6UJKBlYkv27C%2Fselfchaind-linux-amd64?alt=media&token=cd47218e-6562-4553-a63c-62bb1d5199f2
sudo chmod +x selfchaind


# Setup Cosmovisor Symlinks
mkdir -p $HOME/.selfchain/cosmovisor/genesis/bin
mv selfchaind $HOME/.selfchain/cosmovisor/genesis/bin/

sudo ln -s $HOME/.selfchain/cosmovisor/genesis $HOME/.selfchain/cosmovisor/current
sudo ln -s $HOME/.selfchain/cosmovisor/current/bin/selfchaind /usr/local/bin/selfchaind

# Set Service file
sudo tee /etc/systemd/system/selfchaind.service > /dev/null << EOF
[Unit]
Description=selfchaind testnet node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.selfchain"
Environment="DAEMON_NAME=selfchaind"
Environment="UNSAFE_SKIP_BACKUP=true"

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable selfchaind
```

#### Initialize the node <a href="#initialize-the-node" id="initialize-the-node"></a>

```bash
# Set node configuration
selfchaind config chain-id self-dev-1
selfchaind config keyring-backend test
nolusd config node tcp://localhost:11357


# Initialize the node
selfchaind init $MONIKER--chain-id self-dev-1


# Add Genesis File and Addrbook
wget -O $HOME/.selfchain/config/genesis.json  https://raw.githubusercontent.com/hotcrosscom/selfchain-genesis/main/networks/devnet/genesis.json
wget -O $HOME/.selfchain/config/addrbook.json  https://raw.githubusercontent.com/ruangnode/services/main/testnet/self-chain/addrbook.json

#Configure Seeds and Peers
SEEDS="94a7baabb2bcc00c7b47cbaa58adf4f433df9599@157.230.119.165:26656,d3b5b6ca39c8c62152abbeac4669816166d96831@165.22.24.236:26656,35f478c534e2d58dc2c4acdf3eb22eeb6f23357f@165.232.125.66:26656"
PEERS="94a7baabb2bcc00c7b47cbaa58adf4f433df9599@157.230.119.165:26656,d3b5b6ca39c8c62152abbeac4669816166d96831@165.22.24.236:26656,35f478c534e2d58dc2c4acdf3eb22eeb6f23357f@165.232.125.66:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.selfchain/config/config.toml


# Set Pruning, Enable Prometheus, Gas Price, and Indexer
PRUNING="custom"
PRUNING_KEEP_RECENT="100"
PRUNING_INTERVAL="19"

sed -i -e "s/^pruning *=.*/pruning = \"$PRUNING\"/" $HOME/.selfchain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \
\"$PRUNING_KEEP_RECENT\"/" $HOME/.selfchain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \
\"$PRUNING_INTERVAL\"/" $HOME/.selfchain/config/app.toml
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.selfchain/config/config.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.selfchain/config/config.toml
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.005uself\"/" $HOME/.selfchain/config/app.toml

# Set custom ports
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:11358\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:11357\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:11360\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:11356\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":11366\"%" $HOME/.selfchain/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:11317\"%; s%^address = \":8080\"%address = \":11380\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:11390\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:11391\"%; s%:8545%:11345%; s%:8546%:11346%; s%:6065%:11365%" $HOME/.selfchain/config/app.toml

```

**Start service and check the logs**

```
sudo systemctl start selfchaind
sudo journalctl -fu selfchaind -o cat
```

\
\
