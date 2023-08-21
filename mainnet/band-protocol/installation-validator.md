# Installation Validator

**Chain ID**: Band Protocol | **Latest Binary Version**: v2.5.2

Update and install packages for compiling

```
sudo apt update
sudo apt install curl git jq lz4 build-essential -y
```

#### User Management

{% hint style="info" %}
**Running in user** (Assume) : _admin_ We never used this username in our production !
{% endhint %}

Create User, Please Refer to [Broken link](broken-reference "mention")

Login as User salinem

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
echo "Install FHS"
mkdir -p ${HOME}/tmp
mkdir -p ${HOME}/lib
mkdir -p ${HOME}/bin
mkdir -p ${HOME}/conf
mkdir -p ${HOME}/systemd
```

### Install Golang

```bash
# Install Go 1.19.1
wget https://go.dev/dl/go1.19.1.linux-amd64.tar.gz
tar xf go1.19.1.linux-amd64.tar.gz
sudo mv go /usr/local/go

# Set Go path to $PATH variable
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> $HOME/.bashrc
source ~/.bashrc
```

#### Install Band

```bash
cd ~
# Clone BandChain Laozi version v2.5.2
git clone https://github.com/bandprotocol/chain
cd chain
git fetch && git checkout v2.5.2

# Install binaries to $GOPATH/bin
export GOPATH="${HOME}/lib"
make install

cp ${HOME}/lib/bin/* ~/bin/
```

#### Environment

{% hint style="info" %}
direct to ${HOME}/.bashrc or ${HOME}/.profile
{% endhint %}

```
echo 'export PATH="${PATH}:${HOME}/bin"' >> ${HOME}/.bashrc
source ~/.bashrc
```

Set Variable

```bash
# Chain ID of Laozi Mainnet
export CHAIN_ID=laozi-mainnet
# Wallet name to be used as validator's account, please change this into your name (no whitespace).
export WALLET_NAME=<YOUR_WALLET_NAME>
# Name of your validator node, please change this into your name.
export MONIKER=<YOUR_MONIKER>
# URL of genesis file for Laozi Mainnet
export GENESIS_FILE_URL=https://raw.githubusercontent.com/bandprotocol/launch/master/laozi-mainnet/genesis.json
# Data sources/oracle scripts files
export BIN_FILES_URL=https://raw.githubusercontent.com/bandprotocol/launch/master/laozi-mainnet/files.tar.gz
```

#### Intialize Node

```bash
bandd init --chain-id $CHAIN_ID "$MONIKER"
```

**Replace genesis file with our genesis file**

```bash
wget $GENESIS_FILE_URL -O $HOME/.band/config/genesis.json
```

**Download data sources / oracle scripts files, and store in $HOME/.band/files**

```bash
wget -qO- $BIN_FILES_URL | tar xvz -C $HOME/.band/
```

**Create new account**

```bash
bandd keys add $WALLET_NAME
```

Validate Genesis

```
bandd validate-genesis
```

Edit config, You can refer to [Broken link](broken-reference "mention")
