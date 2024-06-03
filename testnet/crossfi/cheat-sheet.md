# Cheat Sheet

Check logs

```bash
sudo journalctl -u crossfid -f
```

Start service

```bash
sudo systemctl start crossfid
```

Stop service

```bash
sudo systemctl stop crossfid
```

Restart service

```bash
sudo systemctl restart crossfid
```

Check service status

```bash
sudo systemctl status crossfid
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable crossfid
```

Disable Service

```bash
sudo systemctl disable crossfid
```

Node info

```bash
crossfid status 2>&1 | jq
```

Your node peer

```bash
echo $(crossfid tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.mineplex-chain/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Key management <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
crossfid keys add $WALLET
```

Restore executing wallet

```bash
crossfid keys add $WALLET --recover
```

List All Wallets

```bash
crossfid keys list
```

Delete wallet

```bash
crossfid keys delete $WALLET
```

Check Balance

```bash
crossfid q bank balances $WALLET_ADDRESS 
```

Export Key (save to wallet.backup)

```bash
crossfid keys export $WALLET
```

View EVM Prived Key

```bash
crossfid keys unsafe-export-eth-key $WALLET
```

Import Key (restore from wallet.backup)

```bash
crossfid keys import $WALLET wallet.backup
```

### Tokens <a href="#tokens" id="tokens"></a>

Withdraw all rewards

```bash
crossfid tx distribution withdraw-all-rewards --from $WALLET --chain-id crossfi-evm-testnet-1 --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx 
```

Withdraw rewards and commission from your validator

```bash
crossfid tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id crossfi-evm-testnet-1 --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx -y 
```

Check your balance

```bash
crossfid query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
crossfid tx staking delegate $(crossfid keys show $WALLET --bech val -a) 1000000mpx --from $WALLET --chain-id crossfi-evm-testnet-1 --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx -y 
```

Delegate

```bash
crossfid tx staking delegate <TO_VALOPER_ADDRESS> 1000000mpx --from $WALLET --chain-id crossfi-evm-testnet-1 --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx -y 	
```

Redelegate Stake to Another Validator

```bash
crossfid tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000mpx --from $WALLET --chain-id crossfi-evm-testnet-1 --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx -y 
```

Unbond

```bash
crossfid tx staking unbond $(crossfid keys show $WALLET --bech val -a) 1000000mpx --from $WALLET --chain-id crossfi-evm-testnet-1 --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx -y 
```

Transfer Funds

```bash
crossfid tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000mpx --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx -y 
```

### Validator operations <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
crossfid tx staking create-validator \
--amount 1000000mpx \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(crossfid tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "" \
--chain-id crossfi-evm-testnet-1 \
--gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx \
-y 
```

Edit Existing Validator

```bash
crossfid tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "" \
--from $WALLET \
--chain-id crossfi-evm-testnet-1 \
--gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx \
-y 
```

Validator info

```bash
crossfid status 2>&1 | jq
```

Validator Details

```bash
crossfid q staking validator $(crossfid keys show $WALLET --bech val -a) 
```

Jailing info

```bash
crossfid q slashing signing-info $(crossfid tendermint show-validator) 
```

Slashing parameters

```bash
crossfid q slashing params 
```

Unjail validator

```bash
crossfid tx slashing unjail --from $WALLET --chain-id crossfi-evm-testnet-1 --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx -y 
```

Active Validators List

```bash
crossfid q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl 
```

Check Validator key

```bash
[[ $(crossfid q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(crossfid status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
crossfid q slashing signing-info $(crossfid tendermint show-validator) 
```

### Governance <a href="#governance" id="governance"></a>

```bash
crossfid  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000mpx \
--type Text \
--from $WALLET \
--gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx \
-y 
```

Proposals List

```bash
crossfid query gov proposals 
```

View proposal

```bash
crossfid query gov proposal 1 
```

Vote

```bash
crossfid tx gov vote 1 yes --from $WALLET --chain-id crossfi-evm-testnet-1  --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx -y 
```
