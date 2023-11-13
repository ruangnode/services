# Start validating

Go to link :

{% embed url="https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Frpc-archive.tangle.tools#/accounts" %}

If you're working on a remote server and need to rotate your session keys, you can use the following command:

```bash
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:9933
```

**Note:** Adjust [http://localhost:9933(opens in a new tab)](http://localhost:9933/) if your node's address differs.

This command prompts your node to generate a new set of session keys. The concatenated public parts of these keys will be returned as the result.

{% hint style="info" %}
Please create 2 wallet address Control and Stash
{% endhint %}
