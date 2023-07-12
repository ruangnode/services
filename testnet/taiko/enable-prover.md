---
description: Optional
---

# Enable Prover

{% hint style="info" %}
Only the first prover per block can earn the TTKO reward, and others will be rejected by the protocol smart contract. This means the fastest prover will be able to prove the block and earn the reward, **if you have just the minimum hardware outlined in the prerequisites below, running a single prover, it's unlikely you will be able to prove any blocks (because you will be competing against other high-performance provers)**.

Meaning, if you do not have a powerful prover, the primary purpose of running a prover is to help test out and provide community feedback on running the node software. Keep in mind this will have a cost in electricity on your computer, and if you are not proving blocks, it's unlikely you will receive a reward to offset that electricity cost.

Please see [How the proof reward is determined](https://taiko.xyz/docs/concepts/proving#how-the-proof-reward-is-determined) for more details.
{% endhint %}

### Prerequisites

* You have already setup a node (see: [Run a node](https://taiko.xyz/docs/guides/run-a-node))
* Must have some ETH on Sepolia (see: [Receive tokens](https://taiko.xyz/docs/guides/receive-tokens)).
* Should have at least 8/16 core CPU and 32GB of RAM. (**note: see warning above**)

### Steps

#### Open `.env` file in `simple-taiko-node`

Open the `.env` file in the `simple-taiko-node` directory.

#### Set environment variables to enable prover

Set the following environment variables to enable your node as a prover:

* Set `ENABLE_PROVER` to `true` (replacing the default `false` with `true`).
* Set `L1_PROVER_PRIVATE_KEY` to that of your wallet's private key; it will need some ETH on Sepolia to prove blocks (if using MetaMask, follow these directions to [retrieve the private key(opens in a new tab)](https://metamask.zendesk.com/hc/en-us/articles/360015289632-How-to-export-an-account-s-private-key)).

#### Verify prover logs

Verify you have some prover logs:

* `💰 Your block proof was accepted` means you are the first prover and receive the reward.
* `✅ Valid block proven` just means a proposed block was successfully proved on TaikoL1 (by anyone).

If you are running a node in the background, you can view the logs with `docker compose logs -f`.
