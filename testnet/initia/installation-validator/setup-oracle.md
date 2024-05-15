# Setup Oracle

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
sudo tee /etc/systemd/system/slinky.service > /dev/null <<EOF
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
sudo systemctl enable slinky.service
sudo systemctl start slinky.service
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
journalctl -fu slinky --no-hostname
```

Successfull Log examples:

```
May 16 01:10:13 slinky[1105498]: {"level":"info","ts":"2024-05-16T01:10:13.596+0200","caller":"oracle/oracle.go:163","msg":"oracle updated prices","pid":1105498,"process":"oracle","last_sync":"2024-05-15T23:10:13.596Z","num_prices":65}
May 16 01:10:13 slinky[1105498]: {"level":"info","ts":"2024-05-16T01:10:13.846+0200","caller":"oracle/oracle.go:163","msg":"oracle updated prices","pid":1105498,"process":"oracle","last_sync":"2024-05-15T23:10:13.846Z","num_prices":65}
May 16 01:10:14 slinky[1105498]: {"level":"info","ts":"2024-05-16T01:10:14.096+0200","caller":"oracle/oracle.go:163","msg":"oracle updated prices","pid":1105498,"process":"oracle","last_sync":"2024-05-15T23:10:14.096Z","num_prices":65}
May 16 01:10:14 slinky[1105498]: {"level":"info","ts":"2024-05-16T01:10:14.346+0200","caller":"marketmap/fetcher.go:116","msg":"successfully fetched market map data from module; checking if market map has changed","pid":1105498,"process":"provider_orchestrator"}
```

> ```
> May 16 01:10:14 slinky[1105498]: {"level":"info","ts":"2024-05-16T01:10:14.346+0200","caller":"marketmap/fetcher.go:116","msg":"successfully fetched market map data from module; checking if market map has changed","pid":1105498,"process":"provider_orchestrator
> "}
> ```
