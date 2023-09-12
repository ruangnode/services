# Command Band

### **Key Management**

{% hint style="info" %}
we assume, variable name\_your\_wallet is name of your own wallet.

Install jq package for management json format

**Ubuntu**

`apt install jq`

**`CentOS`**

`yum install jq`

**Arch Linux**

`pacman -S jq`
{% endhint %}

{% hint style="info" %}
**Running in user** (Assume) : _admin_
{% endhint %}

#### **Add New Key**

```bash
selfchaind keys add mywallet --home ${HOME}/.band     
```

#### **Recover Key**

With Passphrase

```bash
selfchaind keys add mywallet --recover  --home ${HOME}/.band           
```

With Keyring

```bash
selfchaind keys add mywallet --recover --keyring-backend os --home ${HOME}/.band           
```

#### **List Key**

```bash
selfchaind keys list --home ${HOME}/.band        
```

#### **Delete Key**

```bash
selfchaind keys delete mywallet --home ${HOME}/.band 
```

**Export Key**

```bash
 selfchaind keys export mywallet --home ${HOME}/.band 
```

#### Import Key

```
selfchaind keys import mywallet mywallet_file.backup --home ${HOME}/.band
```

#### Show All Balances Address

<pre class="language-bash"><code class="lang-bash"><strong>for wallet in `selfchaind keys list --home ${HOME}/.band--output json| jq -r ".[] .address"`
</strong>do
   CHAIN_ID="gitopia"
   RPC="tcp://localhost:16700"
   bandd q bank balances ${mywallet} --home ${HOME}/.band --chain-id ${CHAIN_ID} --node ${RPC}
done
</code></pre>

#### Show Balance Address

```bash
selfchaind q bank balances mywallet_public_address --home ${HOME}/.band--chain-id laozi-mainnet --node tcp://localhost:16700
```

### Validator Management

{% hint style="info" %}
We assume,You have complete identities. Moniker, Website, Security and Details Your Validator

Install jq package for management json format

**Ubuntu**

`apt install jq`

**`CentOS`**

`yum install jq`

**Arch Linux**

`pacman -S jq`
{% endhint %}

#### Create New Validator

```bash
MONIKER="NAME_OF_YOUR_VALIDATOR"
PROFILE="PGP_KEY_OF_KEYBASE"
DETAILS="Describes Your Validator"
WEBSITE="https://yourwebsite.com"

bandd tx staking create-validator \
--amount=1000000uband \
--pubkey=$(selfchaind tendermint show-validator --home ${HOME}/.selfchain) \
--moniker="${MONIKER}" \
--identity="${PROFILE}" \
--details="${DETAILS}" \
--website="${WEBSITE}" \
--chain-id=self-dev-1 \
--commission-rate=0.05 \
--commission-max-rate=0.20 \
--commission-max-change-rate=0.01 \
--min-self-delegation=1 \
--from=mywallet \
--gas-adjustment=1.4 \
--gas=auto \
--node tcp://localhost:11357 \
--home ${HOME}/.band\
-y
```

#### Edit Validator Identities Informations

```bash
MONIKER="NAME_OF_YOUR_VALIDATOR"
PROFILE="PGP_KEY_OF_KEYBASE"
DETAILS="Describes Your Validator"
WEBSITE="https://yourwebsite.com"

bandd tx staking edit-validator \
--moniker="${MONIKER}" \
--identity="${PROFILE}" \
--details="${DETAILS}" \
--website="${WEBSITE}" \
--chain-id=self-dev-1 \
--commission-rate=0.05 \
--from=mywallet \
--gas-adjustment=1.4 \
--gas=auto \
--node tcp://localhost:11357\
--home ${HOME}/.band\
-y
```

#### Get Validator Info

```
selfchaind status 2>&1 | jq .ValidatorInfo
```

#### Get Syncing Block

```
selfchaind status 2>&1 | jq .SyncInfo
```

#### Get Peer Own Node

```bash
echo $(selfchaind tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.band/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

#### Get Peer Node

```
curl -sS http://localhost:11357/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

#### Unjail Validator

```bash
selfchaind tx slashing unjail \
--from mywallet \
--chain-id self-dev-1 \
 --home ${HOME}/.band\
 --node  tcp://localhost:11357\
 --gas auto \
 --gas-adjustment 1.4 \
 -y
```

#### Jail Reason

<pre class="language-bash"><code class="lang-bash"><strong>selfchaind query slashing signing-info $(selfchaind tendermint show-validator) \
</strong> --node  tcp://localhost:11357\
 --home ${HOME}/.selfchain
</code></pre>

#### List All Active Validator

```bash
selfchaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

#### List All Inactive Validator

```bash
selfchaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

#### Show Validator Details

```
selfchaind \
query staking validator \
$(selfchaind keys show \ 
                $(selfchaind keys list --home ${HOME}/.selfchain --output json| jq -r ".[] .address" | tail -n1) \
--bech val -a) \
--chain-id self-dev \
--node tcp://localhost:11357
```

### Token Management

#### Withdraw All Reward From Validator

```bash
selfchaind tx distribution withdraw-all-rewards \
--from wallet \
--chain-id self-dev-1 \
--node tcp://localhost:11357 \
--home ${HOME}/.selfchain \
--gas-adjustment 1.4 \
--gas auto \
-y
```

#### Withdraw Commision Reward From Validator

```bash
selfchaind tx distribution withdraw-rewards $(selfchaind keys show wallet --bech val -a) \
--commission \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--chain-id self-dev-1 \
--node tcp://localhost:11357 \
--home ${HOME}/.selfchain \
-y 
```

#### Delegate My Token to Own Validator

```bash
selfchaind tx staking delegate $(selfchaind keys show wallet --bech val -a) 1000000uself \
--from wallet \
--gas-adjustment 1.4 \
--home ${HOME}/.selfchain \
--node tcp://localhost:11357 \
--chain-id self-dev-1 \
--gas auto \
-y
```

#### Delegate Your Token To Our Validator

```bash
selfchaind tx staking delegate gravityvaloper1ssduj8c0cc8kquljvw3ygq9hduvcysnf590lmz 1000000uself \ 
--from wallet \
--gas-adjustment 1.4 \ 
--gas auto \
--home ${HOME}/.selfchain \
--node tcp://localhost:11357 \
--chain-id self-dev-1 \
-y
```

#### Redelegate Tokens to Another Validator

```bash
bandd tx staking redelegate $(bandd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uband \
--from mywallet 
--gas-adjustment 1.4 \
--gas auto \
--home ${HOME}/.band\
--node tcp://localhost:16700 \
--chain-id laozi-mainnet \
-y 
```

#### Unbound or Unstake Your Tokens

```bash
bandd tx staking unbond $(bandd keys show wallet --bech val -a) 1000000uband \
--from mywallet \
--gas-adjustment 1.4 \
--gas auto \
--home ${HOME}/.band\
--node tcp://localhost:16700 \
--chain-id laozi-mainnet \
-y 
```

Send tokens to the wallet

```
bandd tx bank send wallet <TO_WALLET_ADDRESS> 1000000uband \
--from mywallet \
--gas-adjustment 1.4 \
--gas auto \
--home ${HOME}/.band\
--node tcp://localhost:16700 \
--chain-id laozi-mainnet \
-y 
```

### Governance

{% hint style="info" %}
Install jq package for management json format

**Ubuntu**

`apt install jq`

**`CentOS`**

`yum install jq`

**Arch Linux**

`pacman -S jq`
{% endhint %}

#### List All Proposal

```bash
bandd query gov proposals
```

#### How to Vote

```bash
### vote yes
bandd tx gov vote 1 yes \
--from mywallet \
--gas-adjustment 1.4 \
--gas auto \
--home ${HOME}/.band\
--node tcp://localhost:16700 \
--chain-id laozi-mainnet \
-y 

### vote no
bandd tx gov vote 1 no \
--from mywallet \
--gas-adjustment 1.4 \
--gas auto \
--home ${HOME}/.band\
--node tcp://localhost:16700 \
--chain-id laozi-mainnet \
-y 

### vote abstain
bandd tx gov vote 1 abstain \
--from mywallet \
--gas-adjustment 1.4 \
--gas auto \
--home ${HOME}/.band\
--node tcp://localhost:16700 \
--chain-id laozi-mainnet \
-y 

### vote No With Veto
bandd tx gov vote 1 nowithveto \
--from mywallet \
--gas-adjustment 1.4 \
--gas auto \
--home ${HOME}/.band\
--node tcp://localhost:16700 \
--chain-id laozi-mainnet \
-y 
```

### Oracle

#### Send Band to Report Wallet

```bash
bandd tx multi-send 1uband $(yoda keys list -a) --gas=1000000 --gas-prices=0.0025uband --gas-adjustment=1.15 \
  --from mywallet --node http://localhost:16700 \
  --chain-id laozi-mainnet
```

#### Add Report wallet to oracle

```bash
bandcli tx oracle add-reporters $(yoda keys list -a) --gas=1000000 --gas-prices=0.0025uband --gas-adjustment=1.15 \
  --from mywallet \
  ---chain-id laozi-mainnet
```

#### Activing Oracle

```bash
bandd tx oracle activate --gas=1000000 --gas-prices=0.0025uband --gas-adjustment=1.15 \
  --from mywallet --node http://localhost:16700 \
  --chain-id laozi-mainnet
```

#### Check Oracle Active

<pre class="language-bash"><code class="lang-bash"><strong>bandd query oracle validator $(bandd keys show -a mywallet --bech val) --chain-id laozi-mainnet --node http://localhost:16700
</strong></code></pre>
