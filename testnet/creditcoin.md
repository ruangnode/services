---
description: Run with docker
---

# CreditCoin

```
docker run -d \
 --name creditcoin-validator \
 -p 30333:30333 \
 -v credit:/creditcoin-node/data  \
 gluwa/creditcoin:2.222.2-testnet \
 --name "namaValidator" \
 --telemetry-url "wss://telemetry.creditcoin.network/submit/ 0" \
 --public-addr "/dns4/IP.VPS.MU/tcp/30333" \
 --chain test \
 --bootnodes "/dns4/testnet-bootnode.creditcoin.network/tcp/30333/p2p/12D3KooWG3eEuYxo37LvU1g6SSESu4i9TQ8FrZmJcjvdys7eA3cH" "/dns4/testnet-bootnode2.creditcoin.network/tcp/30333/p2p/12D3KooWLq7wCMQS3qVMCNJ2Zm6rYuYh74cM99i9Tm8PMdqJPDzb" "/dns4/testnet-bootnode3.creditcoin.network/tcp/30333/p2p/12D3KooWAKUrvmchoLomoouoN1sKfF9kq8dYtCVFvtPuvqp7wFBS" \
 --validator \
 --base-path /creditcoin-node/data \
 --port 30333 
```

> Please change --name with your name validator

check in [EXPLORE](https://telemetry.creditcoin.network/#list/0xc2e43792c8acc075e564558f9a2184a0ffe9b0fd573969599eee9b647358c6cf)
