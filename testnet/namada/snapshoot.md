# Snapshoot

```
sudo systemctl stop namada.service
```

```
cd $HOME/.local/share/namada
```

```
wget http://files.cryptosj.net/files/namadatestnet/namadatestnet.tar.gz
```

```
cp $HOME/.local/share/namada/public-testnet-14.5d79b6958580/cometbft/data/priv_validator_state.json /home/$HOME
```

```
rm -rf public-testnet-14.5d79b6958580/db/
```

```
rm -rf public-testnet-14.5d79b6958580/cometbft/data/
```

```
tar -xvf namadatestnet.tar.gz
```

```
cp $HOME/priv_validator_state.json $HOME/.local/share/namada/public-testnet-14.5d79b6958580/cometbft/data/
```

```
sudo systemctl start namada.service
```

```
sudo journalctl -u namada.service -f --output cat
```

**check node status**

```
curl http://127.0.0.1:26657/status | jq
```
