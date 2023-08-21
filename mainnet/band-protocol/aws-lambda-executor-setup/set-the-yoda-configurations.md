# Set the yoda configurations

#### Set the Yoda configurations[​](https://docs.bandchain.org/node-validators/run-node/joining-mainnet/installation#step-52-set-the-yoda-configurations) <a href="#step-52-set-the-yoda-configurations" id="step-52-set-the-yoda-configurations"></a>

Use the command below to config your Yoda, replacing `$VARIABLES` with their actual values.

```
rm -rf ~/.yoda # clear old config if exist
yoda config chain-id $CHAIN_ID
yoda config node http://localhost:26657
yoda config broadcast-timeout "5m"
yoda config rpc-poll-interval "1s"
yoda config max-try 5
yoda config validator $(bandd keys show $WALLET_NAME -a --bech val)
```

Then, add multiple reporter accounts to allow Yoda to submit transactions concurrently.

```
yoda keys add REPORTER_1
yoda keys add REPORTER_2
yoda keys add REPORTER_3
yoda keys add REPORTER_4
yoda keys add REPORTER_5
```

Lastly, configure the Lambda Executor endpoint to helps running data source scripts and return results to Yoda. More details about the executor can be found in this [section](https://docs.bandchain.org/develop/developer-guides/remote-data-source-executor).

```
export EXECUTOR_URL=<YOUR_EXECUTOR_URL>
yoda config executor "rest:${EXECUTOR_URL}?timeout=10s"
```

#### Start Yoda[​](https://docs.bandchain.org/node-validators/run-node/joining-mainnet/installation#step-53-start-yoda) <a href="#step-53-start-yoda" id="step-53-start-yoda"></a>

To start Yoda, it's also recommended to use `systemctl`.

```
# Write yoda service to /etc/systemd/system/yoda.service
export USERNAME=$(whoami)
sudo -E bash -c 'cat << EOF > /etc/systemd/system/yoda.service
[Unit]
Description=Yoda Daemon
After=network-online.target

[Service]
User=$USERNAME
ExecStart=/home/$USERNAME/go/bin/yoda run
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF'
```

The first time running Yoda, you will need to register and start `yoda` services by running the following commands.

```
# Register yoda to systemctl
sudo systemctl enable yoda
# Start yoda daemon
sudo systemctl start yoda
```

After `yoda` service has been started, logs can be queried by running `journalctl -u yoda.service -f` command. The log should be similar to the following log example below. Once verified, you can stop tailing the log by typing `Control-C`.

```
... systemd[...]: Started Yoda Daemon.
... yoda[...]: I[...] ⭐  Creating HTTP client with node URI: tcp://localhost:26657
... yoda[...]: I[...] 🚀  Starting WebSocket subscriber
... yoda[...]: I[...] 👂  Subscribing to events with query: tm.event = 'Tx'...
```

#### Wait for the latest blocks to be synced[​](https://docs.bandchain.org/node-validators/run-node/joining-mainnet/installation#step-54-wait-for-the-latest-blocks-to-be-synced) <a href="#step-54-wait-for-the-latest-blocks-to-be-synced" id="step-54-wait-for-the-latest-blocks-to-be-synced"></a>

It is imperative to exercise caution and allow adequate time for the newly started BandChain node to synchronize its blocks until it has reached the latest block. The latest block can be verified on [CosmoScan](https://cosmoscan.io/blocks).

Go to Active Oracle session click here [Oracle Activation](../command-band.md#oracle)
