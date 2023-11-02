# Commands

### 🔑 Implicit accounts <a href="#implicit-accounts" id="implicit-accounts"></a>

Namada has multiple account types. An implicit account is used for certain transaction types and is generated from a keypair, similar to accounts on most other blockchains.

**GENERATE A NEW KEY**

```
namada wallet key gen --hd-path default --alias wallet
```

**RECOVER AN EXISTING KEY FROM MNEMONIC**

```
namada wallet key restore --hd-path default --alias wallet
```

**LIST KEYS**

```
namada wallet key list

# list the implicit account address
namada wallet address find --alias wallet
```

### 👛 Established accounts <a href="#established-accounts" id="established-accounts"></a>

In Namada, established accounts are created with a transaction on chain. Their address is known after the on-chain transaction completes. Think of these accounts as extensions of your wallet, allowing you to use all the functionality of Namada.

**INITIALIZE A NEW ACCOUNT**

```
namada client init-account \
  --public-keys wallet \
  --signing-keys wallet \
  --threshold 1 \
  --alias account
```

### 💱 Shielded accounts <a href="#shielded-accounts" id="shielded-accounts"></a>

MASP (the multi-asset shielded pool) enables zero-knowledge transfers without revealing who is involved in the transaction or how much is being sent. Inside the shielded pool, you use a separate key and address from your transparent account. You will create a spending (private) key, which you use as the sender address. Generating a spending key also derives a viewing key, which can be used to see transaction details. To receive transactions, you create a payment (receiver) address. A single spending key can have multiple associated payment addresses.

**GENERATE A NEW SPENDING KEY**

```
namada wallet masp gen-key --alias shielded-key
```

**CREATE A NEW PAYMENT ADDRESS**

```
namada wallet masp gen-addr --key shielded-key --alias shielded-addr
```

**LIST ALL KEYS**

```
# you should keep this viewing key to yourself
namada wallet masp list-keys

# you should really keep this spending key private
namada wallet masp list-keys --unsafe-show-secret --decrypt
```

**LIST ALL ADDRESSES**

```
# you can give the payment address to others so they can transfer to you
namada wallet masp list-addrs
```

### 💱 Transactions <a href="#transactions" id="transactions"></a>

**QUERY ACCOUNT BALANCE**

When checking spending key (shielded account) balance, you see the total across all associated payment addresses.

```
namada client balance --owner account
namada client balance --owner shielded-key

# or query a single token only
namada client balance --token NAM --owner account
namada client balance --token NAM --owner shielded-key
```

**SEND A TRANSACTION**

Generally, the first signing key will be paying the gas fee. A different implicit account can be specified with the `--gas-payer keysha` option. You can also use your shielded account for fees, by providing the `--gas-spending-key shielded-key` option, and (optionally) the `--disposable-gas-payer` option for enhanced privacy (generating a single-use transparent gas payer).

```
# transparent transfer (from transparent account to transparent account)
namada client transfer \
  --signing-keys wallet \
  --source account \
  --target atest1d9khqw368ycrvvjp89rrgse4xycnvdpsxsuyyvp5xvmyv329g9zrzdecgyc5x3pkxaz5zde3gce4dy \
  --token NAM \
  --amount 10
```

```
# shielding transfer (from transparent account to shielded account)
namada client transfer \
  --signing-keys wallet \
  --source account \
  --target shielded-addr \
  --token NAM \
  --amount 10
```

```
# shielded transfer (from shielded account to shielded account)
namada client transfer \
  --signing-keys wallet \
  --source shielded-key \
  --target patest12tpl9w9ya3wlrdwaeuxvcuqufxyzqmxkkhwuf2c3yw5dfrdgtkjs3ee2ksd8zm4wwe7vkvyhd5h \
  --token NAM \
  --amount 10
```

```
# unshielding transfer (from shielded account to transparent account)
namada client transfer \
  --signing-keys wallet \
  --source shielded-key \
  --target account \
  --token NAM \
  --amount 10
```

### 👷 Validator operations <a href="#validator-operations" id="validator-operations"></a>

**CREATE A NEW VALIDATOR**

```
namada client init-validator \
  --alias "YOUR_VALIDATOR_ALIAS" \
  --account-keys wallet \
  --signing-keys wallet \
  --commission-rate 0.05 \
  --max-commission-rate-change 0.01
```

**UNJAIL VALIDATOR**

```
namada client unjail-validator --validator "YOUR_VALIDATOR_ALIAS"
```

**QUERY VALIDATOR STATE**

```
namada client validator-state --validator "YOUR_VALIDATOR_ALIAS"
```

### 💲 PoS staking <a href="#pos-staking" id="pos-staking"></a>

Skip the `--source wallet` option if self-delegating from validator account.

**DELEGATE/BOND TOKENS**

```
namada client bond \
  --source wallet \
  --validator "YOUR_VALIDATOR_ALIAS" \
  --amount 12.34
```

**QUERY DELEGATIONS/BONDS**

```
# to see where a wallet is delegating to
namada client bonds --owner wallet

# to see who is delegating to a validator
namada client bonds --validator "YOUR_VALIDATOR_ALIAS"
```

**UNBOND TOKENS**

```
namada client unbond \
  --source wallet \
  --validator "YOUR_VALIDATOR_ALIAS" \
  --amount 12.34
```

**WITHDRAW UNBONDED TOKENS**

```
namada client withdraw \
  --source wallet \
  --validator "YOUR_VALIDATOR_ALIAS"
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
namada client query-proposal
```

**VIEW PROPOSAL BY ID**

```
namada client query-proposal --proposal-id 0
namada client query-proposal-result --proposal-id 0
```

**VOTE ‘YES’**

```
namada client vote-proposal \
    --proposal-id 0 \
    --vote yay \
    --signing-keys wallet
```

**VOTE ‘NO’**

```
namada client vote-proposal \
    --proposal-id 0 \
    --vote nay \
    --signing-keys wallet
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**UPDATE PORTS**

```
export CUSTOM_PORT_PREFIX=266
sed -i \
  -e "s|^proxy_app = \"tcp://127.0.0.1:26658\"|proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"|" \
  -e "s|^laddr = \"tcp://127.0.0.1:26657\"|laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"|" \
  -e "s|^laddr = \"tcp://0.0.0.0:26656\"|laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"|" \
  -e "s|^prometheus_listen_addr = \":26660\"|prometheus_listen_addr = \":${CUSTOM_PORT}66\"|" \
  $HOME/.local/share/namada/public-testnet-14.5d79b6958580/config.toml
```

**UPDATE INDEXING CONFIGURATION**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.local/share/namada/public-testnet-14.5d79b6958580/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.local/share/namada/public-testnet-14.5d79b6958580/config.toml
```

**UPDATE PRUNING CONFIGURATION**

Pruning is currently not configurable. All nodes are full nodes, starting their history from genesis block.

**RESET NODE**

```
sudo systemctl stop namada.service
namada node ledger reset
```

**DELETE NODE**

Please, note before proceeding with the next step! All data will be lost! Make sure you have backed up your keys!

```
sudo systemctl stop namada.service
sudo systemctl disable namada.service

sudo rm /etc/systemd/system/namada.service
sudo systemctl daemon-reload

rm -f $HOME/.local/bin/namada* $HOME/.local/bin/cometbft
rm -rf $HOME/.local/share/namada
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**ENABLE SERVICE START ON BOOT**

```
sudo systemctl enable namada.service
```

**DISABLE SERVICE START ON BOOT**

```
sudo systemctl disable namada.service
```

**START SERVICE**

```
sudo systemctl start namada.service
```

**STOP SERVICE**

```
sudo systemctl stop namada.service
```

**RESTART SERVICE**

```
sudo systemctl restart namada.service
```

**CHECK SERVICE STATUS**

```
sudo systemctl status namada.service
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u namada.service -f --no-hostname -o cat
```
