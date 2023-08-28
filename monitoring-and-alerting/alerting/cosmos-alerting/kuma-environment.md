# Kuma Environment

{% hint style="info" %}
First, make sure you have installed Kuma Uptime on your server. You can see how to do it [here](../../monitoring-stack/uptime-kuma.md).
{% endhint %}

```
touch band-env
nano band-env
```

```sh
HOST="https://monitoring.ruangnode.com"
HOME_VALIDATOR="/home/user"
DELEGATOR_ADDRESS='band1h8rpvkrpcv8pvycjf7dpqmnpljse93mslkfnv5'
VALIDATOR_ADDRESS='bandvaloper1h8rpvkrpcv8pvycjf7dpqmnpljse93msnqdsfl'
VALIDATOR_RPC="tcp://127.0.0.1:22600"
CHAIN_ID="laozi-mainnet"
BIN="/home/kernel/bin/bandd"
```

{% hint style="info" %}
I am using the example of the Band Protocol sample.
{% endhint %}
