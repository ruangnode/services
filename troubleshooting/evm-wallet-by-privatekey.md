# EVM Wallet by PrivateKey

you can use this tutorial to get UTC file for your wallet from a private key.



```
// install dependencies
apt install npm
npm install ethereumjs-wallet

// create an exporter file
nano export-key-as-json.js
```

then paste all code on the box below

```
const fs = require("fs")
const wallet = require("ethereumjs-wallet").default

const pk = new Buffer.from(process.argv[2], 'hex') // replace by correct private key
const account = wallet.fromPrivateKey(pk)
const password = process.argv[3] // will be required to unlock/sign after importing to a wallet like MyEtherWallet

account.toV3(password)
    .then(value => {
        const address = account.getAddress().toString('hex')
        const file = `UTC--${new Date().toISOString().replace(/[:]/g, '-')}--${address}.json`
        fs.writeFileSync(file, JSON.stringify(value))
    });
```

then use CTRL+X , and save the file.

you can use the following command to generate UTC file by using privatekey and any password you want.

```
// Generate UTC File
node export-key-as-json.js <your-private-key> <some-random-password>
```

{% hint style="info" %}
change the **\<your-private-key>** and **\<some-random-password>** as you need
{% endhint %}
