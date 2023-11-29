# Install Validator

Server Preparation

```
apt update && apt upgrade -y
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

Install GO

```
ver="1.20.3"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```

**Node Installation**

Replace **ruangnode** in the first line with your moniker name.

```
NODE_MONIKER="ruangnode"

cd $HOME
rm -rf sge
git clone https://github.com/sge-network/sge
cd sge
git checkout v1.1.1
make install

sged config chain-id sgenet-1
sged init "$NODE_MONIKER" --chain-id sgenet-1

curl -s https://ss.nodeist.net/sge/genesis.json > $HOME/.sge/config/genesis.json
curl -s https://ss.nodeist.net/sge/addrbook.json > $HOME/.sge/config/addrbook.json

SEEDS=""
PEERS="05628e99f42eb2fbacfd1f0402f96f46b88dfe6b@146.59.52.137:17756,752bc8c7508affd7e2af494a6bf44bcb66cf84ea@65.108.39.140:17756,ba0a167567c7e08f4bb1e25ec24e42a85b07a0c5@148.113.20.208:17756,3fc703341935b9356addfe7b3aad8991d9c8a923@148.113.20.207:17756,9d6916344cea096b7adc86b67faf65f2815f09d3@167.235.39.5:60956,749b2677ed9e05e66f1d5ccf207ba4041e3c7899@15.235.160.196:17756,ad1dce877d93f9de0d3a5c0b0f28d114242c1d3b@64.185.227.122:17756,6c1cbeb621f04886029c7b222041f7fdb307c579@94.130.14.54:17756,073cf41dda08fe3db70b251f0fb466a1149cb01a@67.209.54.167:17756,7258d8c7880167fca502592b8d64110d60e99a6b@65.108.232.180:17756,d32468c30c4dbc9d7d7185b3080d24d25ee1e9cc@65.109.92.148:26656,8f4ca666d56fc883328b1aa0796342c1c1602099@64.185.226.202:17756,13d370cf706e0cbcbed962f7f5585efead848132@158.69.125.73:10456,2eb165895e826174ea95328d83e6bb01d09e46fa@174.138.176.146:26696,401a4986e78fe74dd7ead9363463ba4c704d8759@38.146.3.183:17756,6420509356ba16eb07b785d7f4e8bc9f92f2f375@15.235.160.127:17756,1ea414e1e8cb535e8a91162ac3c3039b86704fee@65.109.92.241:1156,0aa028990c5a135e89447e88daf65a8a590257f4@136.243.67.44:17756,44fe8a028814493ff2a4c71c0da319a5f6149260@162.19.124.59:52656"
sed -i 's|^seeds *=.*|seeds = "'$SEEDS'"|; s|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.sge/config/config.toml

sed -i 's|^pruning *=.*|pruning = "custom"|g' $HOME/.sge/config/app.toml
sed -i 's|^pruning-keep-recent  *=.*|pruning-keep-recent = "100"|g' $HOME/.sge/config/app.toml
sed -i 's|^pruning-interval *=.*|pruning-interval = "10"|g' $HOME/.sge/config/app.toml
sed -i 's|^snapshot-interval *=.*|snapshot-interval = 0|g' $HOME/.sge/config/app.toml

sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0.025usge"|g' $HOME/.sge/config/app.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.sge/config/config.toml

sudo tee /etc/systemd/system/sged.service > /dev/null << EOF
[Unit]
Description=sge node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which sged) start
Restart=on-failure
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF

sged tendermint unsafe-reset-all --home $HOME/.sge --keep-addr-book

curl -L https://ss.nodeist.net/sge/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.sge --strip-components 2


sudo systemctl daemon-reload
sudo systemctl enable sged
sudo systemctl start sged

sudo journalctl -fu sged -o cat
```
