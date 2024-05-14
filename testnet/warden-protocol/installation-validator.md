# Installation Validator

| Chain ID     | Latest Version Tag	 | Wasm |
| ------------ | ------------------- | ---- |
| buenavista-1 | v0.3.0              | on   |

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
ver="1.21.10"
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
# Clone project repository
cd $HOME
rm -rf wardenprotocol
git clone https://github.com/warden-protocol/wardenprotocol.git
cd wardenprotocol
git checkout v0.3.0

# Build binaries
make build

# Prepare binaries for Cosmovisor
mkdir -p $HOME/.warden/cosmovisor/genesis/bin
mv build/wardend $HOME/.warden/cosmovisor/genesis/bin/
rm -rf build

# Create application symlinks
sudo ln -s $HOME/.warden/cosmovisor/genesis $HOME/.warden/cosmovisor/current -f
sudo ln -s $HOME/.warden/cosmovisor/current/bin/wardend /usr/local/bin/wardend -f

# Download and install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0


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
wardend config chain-id buenavista-1
wardend config keyring-backend test
wardend config node tcp://localhost:17857

# Initialize the node
wardend init $MONIKER --chain-id buenavista-1

# Download genesis and addrbook
curl -Ls https://snapshots.kjnodes.com/warden-testnet/genesis.json > $HOME/.warden/config/genesis.json
curl -Ls https://snapshots.kjnodes.com/warden-testnet/addrbook.json > $HOME/.warden/config/addrbook.json

# Add seeds
sed -i -e "s|^seeds *=.*|seeds = \"3f472746f46493309650e5a033076689996c8881@warden-testnet.rpc.kjnodes.com:17859\"|" $HOME/.warden/config/config.toml

# Set minimum gas price
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.0025uward\"|" $HOME/.warden/config/app.toml

# Set pruning
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.warden/config/app.toml

# Set custom ports
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:17858\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:17857\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:17860\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:17856\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":17866\"%" $HOME/.warden/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:17817\"%; s%^address = \":8080\"%address = \":17880\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:17890\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:17891\"%; s%:8545%:17845%; s%:8546%:17846%; s%:6065%:17865%" $HOME/.warden/config/app.toml

```

#### Download latest chain snapshot <a href="#download-latest-chain-snapshot" id="download-latest-chain-snapshot"></a>

```
curl -L https://snapshots.kjnodes.com/warden-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.warden
[[ -f $HOME/.warden/data/upgrade-info.json ]] && cp $HOME/.warden/data/upgrade-info.json $HOME/.warden/cosmovisor/genesis/upgrade-info.json
```

**Start service and check the logs**

```
sudo systemctl start warden.service && sudo journalctl -u warden.service -f --no-hostname -o cat
```

\
\
