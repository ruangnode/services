# Installation

#### Download binaries <a href="#download-binaries" id="download-binaries"></a>

```
mkdir -p $HOME/.local/bin

# CometBFT dependency
curl -sL https://github.com/cometbft/cometbft/releases/download/v0.37.2/cometbft_0.37.2_linux_amd64.tar.gz | tar -xzf- -T<(echo cometbft) -O > $HOME/.local/bin/cometbft
chmod a+x $HOME/.local/bin/cometbft

# Namada binaries
curl -sL https://github.com/anoma/namada/releases/download/v0.23.2/namada-v0.23.2-Linux-x86_64.tar.gz | tar -C $HOME/.local/bin -xzf- --strip-components=1 --exclude='LICENSE*'

```

#### Create SystemD service unit <a href="#create-systemd-service-unit" id="create-systemd-service-unit"></a>

```
sudo tee /etc/systemd/system/namada.service > /dev/null << EOF
[Unit]
Description=Namada node
After=network-online.target

[Service]
User=$USER
ExecStart=$HOME/.local/bin/namada node ledger run
Restart=always
RestartSec=10
LimitNOFILE=65535
Environment="CMT_LOG_LEVEL=p2p:none,pex:error"
Environment="NAMADA_CMT_STDOUT=true"
Environment="NAMADA_LOG=info"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.local/bin"

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable namada.service

```

#### Initialize the node <a href="#initialize-the-node" id="initialize-the-node"></a>

```
export PATH=$HOME/.local/bin:$PATH
namada client utils join-network --chain-id public-testnet-14.5d79b6958580

export CUSTOM_PORT=266
sed -i \
  -e "s|^proxy_app = \"tcp://127.0.0.1:26658\"|proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"|" \
  -e "s|^laddr = \"tcp://127.0.0.1:26657\"|laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"|" \
  -e "s|^laddr = \"tcp://0.0.0.0:26656\"|laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"|" \
  -e "s|^prometheus_listen_addr = \":26660\"|prometheus_listen_addr = \":${CUSTOM_PORT}66\"|" \
  $HOME/.local/share/namada/public-testnet-14.5d79b6958580/config.toml
```

#### Start service and check the logs <a href="#start-service-and-check-the-logs" id="start-service-and-check-the-logs"></a>

```
sudo systemctl start namada.service && sudo journalctl -u namada.service -f --no-hostname -o cat
```
