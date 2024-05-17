# State sync

If you don't want to wait for a long synchronization you can use:

```bash
sudo systemctl stop initiad

cp $HOME/.initia/data/priv_validator_state.json $HOME/.initia/priv_validator_state.json.backup
initiad tendermint unsafe-reset-all --home $HOME/.initia

peers="07632ab562028c3394ee8e78823069bfc8de7b4c@2a01:4f9:3081:3aa3::2:19656,aee7083ab11910ba3f1b8126d1b3728f13f54943@initia-testnet-peer.itrocket.net:11656,dae24101e66118156701ca2ad80b45bf008939a2@158.220.96.33:26656,767fdcfdb0998209834b929c59a2b57d474cc496@207.148.114.112:26656,633775ca828f8fc7f5c689a8c950664e7f198223@184.174.32.188:26656,9f0ae0790fae9a2d327d8d6fe767b73eb8aa5c48@176.126.87.65:22656,7317b8c930c52a8183590166a7b5c3599f40d4db@185.187.170.186:26656,c1d07588f2e116090e43dbbd58f34b2b449ca518@194.163.174.193:26656,a45314423c15f024ff850fad7bd031168d937931@162.62.219.188:26656,35e4b461b38107751450af25e03f5a61e7aa0189@43.133.229.136:26656,531a4d0d5daa67812aa4ab9953fdd49ab75d5e73@49.13.115.206:15656,6a64518146b8c902ef5930dfba00fe61a15ec176@43.133.44.152:26656"  
SNAP_RPC="https://initia.rpc.ruangnode.com:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.initia/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.initia/config/config.toml

mv $HOME/.initia/priv_validator_state.json.backup $HOME/.initia/data/priv_validator_state.json

sudo systemctl restart initiad && sudo journalctl -u initiad -f
```
