---
description: Optional
---

# Enable a proposer

1.  #### Visit the TaikoL1 contract on Sepolia Etherscan

    Navigate to the [TaikoL1 contract(opens in a new tab)](https://sepolia.etherscan.io/address/0x6375394335f34848b850114b66A49D6F47f2cdA8#writeProxyContract) on Sepolia.

    Click `Connect to Web3` and connect your wallet.\

2.  #### Enter deposit amount

    Click `depositTaikoToken` and enter the amount of TTKO you would like to deposit followed by 8 zeroes.

{% hint style="info" %}
⚠️ Make sure to click the plus sign and add `10^8` decimals, or add 8 zeroes manually.
{% endhint %}

For example if you want to deposit `69` TTKO, you would enter `6900000000`.

3.  #### Deposit your TTKO

    Click `Write` and confirm the transaction in your wallet.\

4.  #### Open `.env` file in `simple-taiko-node`

    Open the `.env` file in the `simple-taiko-node` directory.\

5.  #### Set environment variables to enable a proposer

    Set the following environment variables to enable your node as a proposer:

    * Set `ENABLE_PROPOSER` to `true` (replacing the default `false` with `true`).
    * Set `L1_PROPOSER_PRIVATE_KEY` to that of your wallet's private key; it will need some TTKO on Sepolia to propose blocks (if using MetaMask, follow these directions to [retrieve the private key(opens in a new tab)](https://metamask.zendesk.com/hc/en-us/articles/360015289632-How-to-export-an-account-s-private-key)).
    * Set `L2_SUGGESTED_FEE_RECIPIENT` to the recipient of L2 ETH rewards.\

6.  #### Verify proposer logs

    You should see a log if you have proposed a block: `📝 Propose transactions succeeded`.

    If you are running a node in the background, you can view the logs with `docker compose logs -f`.

\


For example if you want to deposit `69` TTKO, you would enter `6900000000`.

