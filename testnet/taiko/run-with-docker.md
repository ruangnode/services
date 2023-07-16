# Run with Docker

```
git clone https://github.com/taikoxyz/simple-taiko-node.git 
cd simple-taiko-node
```

First, copy the `.env.sample` to a new file `.env`:

```
cp .env.sample .env
```

```
nano .env
```

Please input your HTTP and WSS&#x20;

{% hint style="info" %}
You can get a Sepolia L1 endpoint from a few places, but it **must point to an archive node** to access the state trie beyond the last 128 blocks.

**Recommended**: Run your own Sepolia archive node, see: [Run a Sepolia node](https://taiko.xyz/docs/guides/run-a-sepolia-node). This is recommended because you will eventually be rate-limited by public RPC providers.

**Alternative**: [Alchemy(opens in a new tab)](https://www.alchemy.com/) and [Infura(opens in a new tab)](https://www.infura.io/) are two popular RPC providers. Make sure you select the RPC as Sepolia testnet, and not Ethereum mainnet.
{% endhint %}
