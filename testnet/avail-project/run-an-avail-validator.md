---
description: Data Availability Deployments
---

# Run an Avail Validator

{% hint style="info" %}
ONBOARDING VALIDATORS

Please join our Discord for up-to-date information on our network and validator onboarding.
{% endhint %}

{% hint style="warning" %}
<mark style="background-color:red;">**SYSTEM ADMINISTRATION**</mark>

<mark style="background-color:red;">Although Avail is in testnet phase, in general, users should have significant system administration experience when running validator nodes.</mark>

<mark style="background-color:red;">Validator nodes are responsbile for maintaining and securing the network by staking tokens with real value. Validators need to understand how to manage their node, its associated hardware & configuration, and be wary that they are subject to being slashed due to actions like being offline or equivocation.</mark>

<mark style="background-color:red;">When in doubt, reach out to the Validator Engagement team.</mark>
{% endhint %}

Update and install packages for compiling

```sh
sudo apt update
sudo apt install make clang pkg-config libssl-dev build-essential
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

and make sure we are in directory

```bash
pwd

/home/admin
```

{% hint style="info" %}
**Running in user** : admin
{% endhint %}

#### Create directory

```bash
mkdir -p ${HOME}/avail-node
mkdir -p ${HOME}/avail-node/data
mkdir -p ${HOME}/avail-node/systemd
```

**Download The Required Files**

go to dir **/**_**avail-node**_ for the setup validator

```
cd avail-node
```

```sh
wget https://github.com/availproject/avail/releases/download/v1.7.2/data-avail-linux-amd64.tar.gz
```

```
wget https://availproject.github.io/assets/files/chainspec.raw-1905ee8ba4620c9cd6f1f378a84346eb.json
```

**Extract File**

```
tar -xvzf data-avail-linux-amd64.tar.gz
```

```
cp data-avail-linux-amd64 data-avail
```

```
cp chainspec.raw-1905ee8ba4620c9cd6f1f378a84346eb.json chainspec.raw.json
```

**Create Systemd**

```
export USERNAME=$(whoami)
cat > ${HOME}/avail-node/systemd/availd.service  <<EOF

[Unit]
Description=Avail Validator
After=network.target
StartLimitIntervalSec=0

[Service]
User=$USERNAME
Type=simple
Restart=always
RestartSec=120
ExecStart=${HOME}/avail-node/data-avail --base-path ${HOME}/avail-node/data --chain ${HOME}/avail-node/chainspec.raw.json --port 30333 --validator --name "YOUR_NAME_VALIDATOR"

[Install]
WantedBy=multi-user.target

EOF
```

{% hint style="info" %}
please use the second one if **chainspec.raw.json** is not available.
{% endhint %}

```
export USERNAME=$(whoami)
cat > ${HOME}/avail-node/systemd/availd.service  <<EOF

[Unit]
Description=Avail Validator
After=network.target
StartLimitIntervalSec=0

[Service]
User=$USERNAME
Type=simple
Restart=always
RestartSec=120
ExecStart=${HOME}/avail-node/data-avail --base-path ${HOME}/avail-node/data --chain kate --port 30333 --validator --name "YOUR_NAME_VALIDATOR"

[Install]
WantedBy=multi-user.target

EOF
```

Linking to Systemd

```bash
sudo ln -sf ${HOME}/avail-node/systemd/availd.service /etc/systemd/system/
```

Reload Daemon

```bash
sudo systemctl daemon-reload
```

Enable Service when booting

```bash
sudo systemctl enable availd.service
```

Start Service

```bash
sudo systemctl start availd.service
```

Check Service

```bash
sudo systemctl status availd.service
sudo journalctl -fu availd.service
```

The node will ouput the following when started:

```
2023-06-03 20:36:29 Avail Node
2023-06-03 20:36:29 ✌️  version 1.6.0-99b85257d6b
2023-06-03 20:36:29 ❤️  by Anonymous, 2017-2023
2023-06-03 20:36:29 📋 Chain specification: Avail Kate Testnet
2023-06-03 20:36:29 🏷  Node name: bewildered-distance-1229
2023-06-03 20:36:29 👤 Role:Authority
2023-06-03 20:36:29 💾 Database: RocksDb at /Users/thunder/code/avail/data/chains/Avail Testnet_6831251e-0222-11ee-a2c3-c90377335962/db/full
2023-06-03 20:36:29 ⛓  Native runtime: data-avail-9 (data-avail-0.tx1.au11)
2023-06-03 20:36:35 👶 Creating empty BABE epoch changes on what appears to be first startup.
2023-06-03 20:36:35 🏷  Local node identity is: 12D3KooWPt7odw3aeq7azZDugXjNuUvQNPU58n1VRBzY1YBqsjkr
2023-06-03 20:36:35 Prometheus metrics extended with avail metrics
2023-06-03 20:36:35 💻 Operating system: macos
2023-06-03 20:36:35 💻 CPU architecture: aarch64
2023-06-03 20:36:35 📦 Highest known block at #0
2023-06-03 20:36:35 〽️ Prometheus exporter started at 127.0.0.1:9615
2023-06-03 20:36:35 Running JSON-RPC HTTP server: addr=127.0.0.1:9933, allowed origins=["http://localhost:*", "http://127.0.0.1:*", "https://localhost:*", "https://127.0.0.1:*", "https://polkadot.js.org"]
2023-06-03 20:36:35 Running JSON-RPC WS server: addr=127.0.0.1:9944, allowed origins=["http://localhost:*", "http://127.0.0.1:*", "https://localhost:*", "https://127.0.0.1:*", "https://polkadot.js.org"]
2023-06-03 20:36:35 🏁 CPU score: 724.71 MiBs
2023-06-03 20:36:35 🏁 Memory score: 41.49 GiBs
2023-06-03 20:36:35 🏁 Disk score (seq. writes): 1.91 GiBs
2023-06-03 20:36:35 🏁 Disk score (rand. writes): 454.66 MiBs
```

