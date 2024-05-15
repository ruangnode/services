# Installation Validator

| Chain ID     | Latest Version Tag	 | Wasm |
| ------------ | ------------------- | ---- |
| initiation-1 | v0.2.14             | off  |

Update and install packages for compiling

```
# install dependencies, if needed
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

### Installation

<pre class="language-bash"><code class="lang-bash"><strong># install go, if needed
</strong><strong>cd $HOME
</strong>VER="1.22.2"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] &#x26;&#x26; touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] &#x26;&#x26; mkdir -p ~/go/bin


# set vars
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export INITIA_CHAIN_ID="initiation-1"" >> $HOME/.bash_profile
echo "export INITIA_PORT="19"" >> $HOME/.bash_profile
source $HOME/.bash_profile

# download binary
cd $HOME
mkdir lib
git clone https://github.com/initia-labs/initia
cd initia
git checkout v0.2.14
export GOPATH="${HOME}/lib"
make install

cp ${HOME}/lib/bin/* ~/go/bin/

# config and init app
initiad init $MONIKER --chain-id initiation-1
sed -i -e "s|^node *=.*|node = \"tcp://localhost:${INITIA_PORT}657\"|" $HOME/.initia/config/client.toml

# download genesis and addrbook
curl -Ls https://testnet-file.ruangnode.com/snap-testnet/initia-testnet/genesis.json > $HOME/.initia/config/genesis.json
curl -Ls https://testnet-file.ruangnode.com/snap-testnet/initia-testnet/addrbook.json > $HOME/.initia/config/addrbook.json

# set seeds and peers
PEERS="093e1b89a498b6a8760ad2188fbda30a05e4f300@35.240.207.217:26656"
SEEDS="2eaa272622d1ba6796100ab39f58c75d458b9dbc@34.142.181.82:26656,c28827cb96c14c905b127b92065a3fb4cd77d7f6@testnet-seeds.whispernode.com:25756"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.initia/config/config.toml

# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${INITIA_PORT}317%g;
s%:8080%:${INITIA_PORT}080%g;
s%:9090%:${INITIA_PORT}090%g;
s%:9091%:${INITIA_PORT}091%g;
s%:8545%:${INITIA_PORT}545%g;
s%:8546%:${INITIA_PORT}546%g;
s%:6065%:${INITIA_PORT}065%g" $HOME/.initia/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${INITIA_PORT}658%g;
s%:26657%:${INITIA_PORT}657%g;
s%:6060%:${INITIA_PORT}060%g;
s%:26656%:${INITIA_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${INITIA_PORT}656\"%;
s%:26660%:${INITIA_PORT}660%g" $HOME/.initia/config/config.toml

# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.initia/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.initia/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.initia/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.15uinit,0.01uusdc\"|" $HOME/.initia/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.initia/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.initia/config/config.toml

# create service file\
sudo tee /etc/systemd/system/initiad.service > /dev/null &#x3C;&#x3C;EOF
[Unit]
Description=Initia node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.initia
ExecStart=$(which initiad) start --home $HOME/.initia
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# reset and download snapshot
curl -L https://testnet-file.ruangnode.com/snap-testnet/initia-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.initia
[[ -f $HOME/.initia/data/upgrade-info.json ]] &#x26;&#x26; cp $HOME/.initia/data/upgrade-info.json $HOME/.initia/cosmovisor/genesis/upgrade-info.json

# enable and start service
sudo systemctl daemon-reload
sudo systemctl enable initiad
sudo systemctl restart initiad &#x26;&#x26; sudo journalctl -u initiad -f
</code></pre>

**Create Wallet**

```bash
# to create a new wallet, use the following command. don’t forget to save the mnemonic
initiad keys add $WALLET

# to restore exexuting wallet, use the following command
initiad keys add $WALLET --recover

# save wallet and validator address
WALLET_ADDRESS=$(initiad keys show $WALLET -a)
VALOPER_ADDRESS=$(initiad keys show $WALLET --bech val -a)
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile

# check sync status, once your node is fully synced, the output from above will print "false"
initiad status 2>&1 | jq 

# before creating a validator, you need to fund your wallet and check balance
initiad query bank balances $WALLET_ADDRESS 
```

**Create Validator**

```bash
initiad tx mstaking create-validator \
--amount 1000000uinit \
--pubkey $(initiad tendermint show-validator) \
--moniker "$MONIKER" \
--identity "YOUR_IDENTITY" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE" \
--chain-id initiation-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--from $WALLET \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.15uinit \
-y
```

## Set up Oracle <a href="#set-up-oracle" id="set-up-oracle"></a>

This guide is only for validator nodes

Official documentation: [https://docs.initia.xyz/run-initia-node/running-initia-node/oracle](https://docs.initia.xyz/run-initia-node/running-initia-node/oracle)

The Slinky Oracle consists of two main elements:

1. An on-chain component that retrieves price data from the sidecar with each block, forwards these prices to the blockchain through vote extensions, and compiles prices from all validators involved.
2. A sidecar process dedicated to polling price information from various providers and delivering this data to the on-chain component.

#### Clone the Repository and build binaries <a href="#step-1-clone-the-repository-and-build-binaries" id="step-1-clone-the-repository-and-build-binaries"></a>

```
# Clone repository
cd $HOME
rm -rf slinky
git clone https://github.com/skip-mev/slinky.git
cd slinky
git checkout v0.4.3

# Build binaries
make build

# Move binary to local bin
mv build/slinky /usr/local/bin/
rm -rf build
```

#### Run oracle <a href="#step-2-run-oracle" id="step-2-run-oracle"></a>

#### Created systemd services <a href="#step-2-run-oracle" id="step-2-run-oracle"></a>

```
sudo tee /etc/systemd/system/slinkyd.service > /dev/null <<EOF
[Unit]
Description=Initia Slinky Oracle
After=network-online.target

[Service]
User=$USER
ExecStart=$(which slinky) --oracle-config-path $HOME/slinky/config/core/oracle.json --market-map-endpoint 0.0.0.0:19090
Restart=on-failure
RestartSec=30
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

#### Enable and start systemd service <a href="#step-2-run-oracle" id="step-2-run-oracle"></a>

```
sudo systemctl daemon-reload
sudo systemctl enable slinkyd.service
sudo systemctl start slinkyd.service
```

#### Validating Prices <a href="#step-3-validating-prices" id="step-3-validating-prices"></a>

Upon launching the oracle, you should observe successful price retrieval from the provider sources. Additionally, you have the option to execute the test client script available in the Slinky repository by using the command:

```
make run-oracle-client
```

#### Enable Oracle Vote Extension <a href="#step-4-enable-oracle-vote-extension" id="step-4-enable-oracle-vote-extension"></a>

In order to utilize the Slinky oracle data in the Initia node, the Oracle setting should be enabled in the config/app.toml file.

```
###############################################################################
###                                  Oracle                                 ###
###############################################################################
[oracle]
# Enabled indicates whether the oracle is enabled.
enabled = "true"

# Oracle Address is the URL of the out of process oracle sidecar. This is used to
# connect to the oracle sidecar when the application boots up. Note that the address
# can be modified at any point, but will only take effect after the application is
# restarted. This can be the address of an oracle container running on the same
# machine or a remote machine.
oracle_address = "127.0.0.1:8080"

# Client Timeout is the time that the client is willing to wait for responses from 
# the oracle before timing out.
client_timeout = "500ms"

# MetricsEnabled determines whether oracle metrics are enabled. Specifically
# this enables instrumentation of the oracle client and the interaction between
# the oracle and the app.
metrics_enabled = "false"
```

#### Check the systemd logs <a href="#step-5-check-the-systemd-logs" id="step-5-check-the-systemd-logs"></a>

To check service logs use command below:

```
journalctl -fu slinkyd --no-hostname
```

Successfull Log examples:

```
May 16 01:10:13 slinky[1105498]: {"level":"info","ts":"2024-05-16T01:10:13.596+0200","caller":"oracle/oracle.go:163","msg":"oracle updated prices","pid":1105498,"process":"oracle","last_sync":"2024-05-15T23:10:13.596Z","num_prices":65}
May 16 01:10:13 slinky[1105498]: {"level":"info","ts":"2024-05-16T01:10:13.846+0200","caller":"oracle/oracle.go:163","msg":"oracle updated prices","pid":1105498,"process":"oracle","last_sync":"2024-05-15T23:10:13.846Z","num_prices":65}
May 16 01:10:14 slinky[1105498]: {"level":"info","ts":"2024-05-16T01:10:14.096+0200","caller":"oracle/oracle.go:163","msg":"oracle updated prices","pid":1105498,"process":"oracle","last_sync":"2024-05-15T23:10:14.096Z","num_prices":65}
May 16 01:10:14 slinky[1105498]: {"level":"info","ts":"2024-05-16T01:10:14.346+0200","caller":"marketmap/fetcher.go:116","msg":"successfully fetched market map data from module; checking if market map has changed","pid":1105498,"process":"provider_orchestrator"}
```

{% hint style="info" %}
```
May 16 01:10:14 slinky[1105498]: {"level":"info","ts":"2024-05-16T01:10:14.346+0200","caller":"marketmap/fetcher.go:116","msg":"successfully fetched market map data from module; checking if market map has changed","pid":1105498,"process":"provider_orchestrator"}
```
{% endhint %}
