# Full node setup

**Install depedencies**

```bash
sudo apt install --assume-yes git clang curl libssl-dev llvm libudev-dev make protobuf-compiler
```

**User management**

{% hint style="info" %}
```
Running in user (Assume) : admin We never used this username in our production !
```
{% endhint %}

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

#### FHS of Band

Create FHS for application

```bash
mkdir -p ${HOME}/bin
mkdir -p ${HOME}/systemd
mkdir -p ${HOME}/chainspecs
mkdir -p ${HOME}/nodekey
```

**Download binary latest version**

```
cd /bin
wget -O tangle https://github.com/webb-tools/tangle/releases/download/v0.5.0/tangle-standalone-linux-amd64
mv tangle-standalone-linux-amd64 tangle-standalone
```

**Download chainspecs**

```
cd  chainspecs
curl -O https://raw.githubusercontent.com/webb-tools/tangle/main/chainspecs/testnet/tangle-standalone.json
```

**Generate & store keys**

{% hint style="info" %}
* DKG key (Ecdsa)
* Aura key (Sr25519)
* Account key (Sr25519)
* Grandpa key (Ed25519)
* ImOnline key (Sr25519)
{% endhint %}

**Account Keys**

```
# it will ask for your suri, enter it.
./tangle-standalone key insert --base-path $HOME/nodekey \
--chain $HOME/chainspecs/tangle-standalone.json \
--scheme Sr25519 \
--suri <"12-MNEMONIC-PHARSE"> \
--key-type acco
```

**Aura Keys**

```
# it will ask for your suri, enter it.
./target/release/tangle-standalone key insert --base-path /data/validator/<USERNAME> \
--chain ./chainspecs/tangle-standalone.json \
--scheme Sr25519 \
--suri <"12-MNEMONIC-PHARSE"> \
--key-type aura
```

**Im-online Keys** - **these keys are optional (required if you are running as a validator)**

```
# it will ask for your suri, enter it.
./tangle-standalone key insert --base-path $HOME/nodekey \
--chain $HOME/chainspecs/tangle-standalone.json \
--scheme Sr25519 \
--suri <"12-MNEMONIC-PHARSE"> \
--key-type imon
```

**DKG Keys**

```
# it will ask for your suri, enter it.
./tangle-standalone key insert --base-path $HOME/nodekey \
--chain $HOME/chainspecs/tangle-standalone.json \
--scheme Ecdsa \
--suri <"12-MNEMONIC-PHARSE"> \
--key-type wdkg
```

**Grandpa Keys**

```
# it will ask for your suri, enter it.
./tangle-standalone key insert --base-path $HOME/nodekey \
--chain $HOME/chainspecs/tangle-standalone.json \
--scheme Ed25519 \
--suri <"12-MNEMONIC-PHARSE"> \
--key-type gran
```

**Check file in directory chainspecs**

```
$ ls -lrth
total 20K
-rw------- 1 tangle tangle 81 Nov 11 16:55 6163636fc87cebadaa996229851198268d87f1f171e96a5efa008174f60f4d1d5037336e
-rw------- 1 tangle tangle 81 Nov 11 16:58 61757261c87cebadaa996229851198268d87f1f171e96a5efa008174f60f4d1d5037336e
-rw------- 1 tangle tangle 81 Nov 11 16:58 696d6f6ec87cebadaa996229851198268d87f1f171e96a5efa008174f60f4d1d5037336e
-rw------- 1 tangle tangle 81 Nov 11 16:59 77646b6703c856bf8c9249fead8b0c7c966db9e61efc537f587bbc08b9444977e51fc6ac25
-rw------- 1 tangle tangle 81 Nov 11 17:00 6772616e02de83d9c3713577eef584cd0c2b49c68750e0916bf09481acb38f0182066cec
```

**Create systemd**

```
cd system

export USERNAME=$(whoami)
cat > ${HOME}/systemd/tangled.service  <<EOF
[Unit]
Description=Tangle Validator Node
After=network-online.target
StartLimitIntervalSec=0

[Service]
User=tangle
Restart=always
RestartSec=3
ExecStart=${HOME}/bin/tangle-standalone \
  --base-path ${HOME}/validator/ruangnode \
  --name <NAME_VALIDATOR \
  --chain tangle-testnet \
  --node-key-file "${HOME}/node-key" \
  --port 30333 \
  --validator \
  --no-mdns \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0"

[Install]
WantedBy=multi-user.target
```

**Enabled service**

```
sudo systemctl daemon-reload
sudo systemctl enable tangled.service
sudo systemctl start tangled.service

#Check status
sudo systemctl status tangled.service
sudo journalctl -u tangled.service -f
```

**If the node is running correctly, you should see an output similar to below:**

```
Nov 13 03:42:00 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:00 [#770102] 🗳  Starting phase Off, round 314.
Nov 13 03:42:00 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:00 [770102] 💸 new validator set of size 5 has been processed for era 314
Nov 13 03:42:01 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:01 [770350] 💸 generated 5 npos targets
Nov 13 03:42:01 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:01 [770350] 💸 generated 7 npos voters, 5 from validators and 2 nominators
Nov 13 03:42:01 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:01 [#770350] 🗳  creating a snapshot with metadata SolutionOrSnapshotSize { voters: 7, targets: 5 }
Nov 13 03:42:01 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:01 [#770350] 🗳  Starting phase Signed, round 314.
Nov 13 03:42:01 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:01 [#770375] 🗳  Starting phase Unsigned((true, 770375)), round 314.
Nov 13 03:42:01 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:01 [#770376] 🗳  queued unsigned solution with score ElectionScore { minimal_stake: 5421, sum_stake: 27105, sum_stake_squared: 146936205 }
Nov 13 03:42:03 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:03 ⚙️  Syncing 398.0 bps, target=#1403810 (9 peers), best: #771022 (0xf5b7…60c2), finalized #770560 (0xc6d4…7f08), ⬇ 136.7kiB/s ⬆ 2.3kiB/s
Nov 13 03:42:08 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:08 ⚙️  Syncing 390.6 bps, target=#1403811 (9 peers), best: #772975 (0x59e5…3955), finalized #772608 (0x3bfd…da3f), ⬇ 122.7kiB/s ⬆ 5.7kiB/s
Nov 13 03:42:10 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:10 [#773714] 🗳  Finalized election round with compute Unsigned.
Nov 13 03:42:10 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:10 [#773714] 🗳  Starting phase Off, round 315.
Nov 13 03:42:10 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:10 [773714] 💸 new validator set of size 5 has been processed for era 315
Nov 13 03:42:10 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:10 [773950] 💸 generated 5 npos targets
Nov 13 03:42:10 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:10 [773950] 💸 generated 7 npos voters, 5 from validators and 2 nominators
Nov 13 03:42:10 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:10 [#773950] 🗳  creating a snapshot with metadata SolutionOrSnapshotSize { voters: 7, targets: 5 }
Nov 13 03:42:10 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:10 [#773950] 🗳  Starting phase Signed, round 315.
Nov 13 03:42:11 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:10 [#773975] 🗳  Starting phase Unsigned((true, 773975)), round 315.
Nov 13 03:42:11 ruangnode-testnet tangle-standalone[2180891]: 2023-11-13 03:42:10 [#773976] 🗳  queued unsigned solution with score ElectionScore { minimal_stake: 5421, sum_stake: 27105, sum_stake_squared: 146936205 }
```

{% hint style="info" %}
If you get error please check your data keystore in **base-path** directory.\
\
location here **--base-path ${HOME}/validator/ruangnode**\
Copy all-file **storekeys** to **base-path**
{% endhint %}
