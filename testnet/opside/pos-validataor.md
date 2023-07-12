# PoS Validataor

## Ubuntu(Recommended)

**OS:** Ubuntu 20.04 (amd64) and above, you can download it here [https://releases.ubuntu.com/20.04.6/ubuntu-20.04.6-live-server-amd64.iso](https://releases.ubuntu.com/20.04.6/ubuntu-20.04.6-live-server-amd64.iso)​

### Port configuration <a href="#port-configuration" id="port-configuration"></a>

| Port/protocol | Firewall rule                       | Reason/caveats                                                                                                                                                                                                                                                                                               |
| ------------- | ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 8545/TCP      | Block all traffic.                  | This is the JSON-RPC port for your execution node's Query API. You (and apps) can use this port to check execution node status, query execution-layer chain data, and even submit transactions. This port generally shouldn't be exposed to the outside world.                                               |
| 3500/TCP      | Block all traffic.                  | This is the JSON-RPC port for your beacon node's Query API. You (and apps) can use this port to check beacon node status and query consensus-layer chain data. This port generally shouldn't be exposed to the outside world.                                                                                |
| 8551/TCP      | Block all traffic.                  | Your beacon node connects to your execution node's [Engine API](https://github.com/ethereum/execution-apis/blob/main/src/engine/specification.md) using this port. Inbound and outbound traffic should be allowed through this port only if your local beacon node is connecting to a remote execution node. |
| 4000/TCP      | Block all traffic.                  | Your validator uses this port to connect to your beacon node via [gRPC](https://grpc.io/). Inbound and outbound traffic should be allowed through this port only if your local validator is connecting to a remote beacon node.                                                                              |
| \*/UDP+TCP    | Allow outbound traffic.             | To [discover](https://github.com/ethereum/devp2p/wiki/Discovery-Overview) peers, Prysm's beacon node dials out through random ports. Allowing outbound TCP/UDP traffic from any port will help Prysm find peers.                                                                                             |
| 13000/TCP     | Allow inbound and outbound traffic. | After we discover peers, we dial them through this port to establish an ongoing connection for [libp2p](https://libp2p.io/) and through which all gossip/p2p request and responses will flow.                                                                                                                |
| 12000/UDP     | Allow inbound and outbound traffic. | Your beacon node exposes this UDP port so that other Ethereum nodes can discover your node, request chain data, and provide chain data.                                                                                                                                                                      |
| 33687/TCP+UDP | Allow inbound and outbound traffic. | 33687/TCP is your execution node's listener port, while 33687/UDP is its discovery port. This rule lets your execution node connect to other peers.                                                                                                                                                          |

### Install auto-program <a href="#install-auto-program" id="install-auto-program"></a>

Checkpoint sync (recommended)Genesis syncWith checkpoint sync configured, your beacon node will begin syncing from a recently finalized checkpoint instead of syncing from genesis. This can make installations, validator migrations, recoveries, and network deployments _way_ faster.

```
wget -c https://pre-alpha-download.opside.network/testnet-auto-install-v2.tar.gz 
tar -C ./ -xzf testnet-auto-install-v2.tar.gz
chmod +x -R ./testnet-auto-install-v2
cd ./testnet-auto-install-v2
```

### Install validator clients <a href="#install-validator-clients" id="install-validator-clients"></a>

```
./install-ubuntu-en-1.0.sh
```

Follow the CLI prompts to generate your keys. You will need to enter:1. Your withdrawal Opside address(which is used to receive your validator rewards and your deposit when you exit)2. Password(which is used to encrypt your validator signing key)3. Repeat your withdrawal Opside address4. Repeat your PasswordThen there will be 24 mnemonic seed phrases. This is highly sensitive and should never be exposed to other people or networked hardware.

* You should now have your mnemonic written down in a safe place and a keystore saved for each of your validators.
* Please make sure you keep these safe, preferably offline.

Follow the CLI prompts to:

* Enter your seed phrase(separated with space)
* Waiting for the validator key generated
* Waiting for the nodes launched

### Check the logs <a href="#check-the-logs" id="check-the-logs"></a>

```
# show the execution client logs
opside-chain/show-geth-log.sh

# show the consensus client logs
opside-chain/show-beaconChain-log.sh

# show the validator logs
opside-chain/show-validator-log.sh
```

{% hint style="info" %}
After client installed, ensure you are fully synced before submitting your staking deposit. This can take several days.
{% endhint %}

#### Example of geth logs <a href="#example-of-geth-logs" id="example-of-geth-logs"></a>

```
# show the execution client logs
opside-chain/show-geth-log.sh
```

You will see:

```
INFO [05-01|18:14:25.789] Looking for peers peercount=5 tried=3 static=6
INFO [05-01|18:14:36.039] Imported new potential chain segment number=48883 hash=5ec950..75d245 blocks=1 txs=0
mgas=0.000 elapsed=530.471µs mgasps=0.000 dirty=2.28MiB
INFO [05-01|18:14:36.041] Chain head was updated number=48883 hash=5ec950..75d245 root=5b148e..5d226a elapsed=97.462µs
```

[Check here](https://pre-alpha.opside.info/) to see if the block height is close to the\[number=48883], if yes, it's time for IDE to be deposited.

### Deposit <a href="#deposit" id="deposit"></a>

#### Upload deposit file <a href="#upload-deposit-file" id="upload-deposit-file"></a>

Go to [Validator Launchpad](https://opside.network/validator/deposit), follow the steps to enter the _Upload deposit data_ page, then upload the deposit data file you just generated.The `deposit_data-[timestamp].json` is located in directory `testnet-auto-install/validator_keys/`.

{% hint style="info" %}
For MacOS, the `deposit_data-[timestamp].json` located in directory `opside-staking-deposit-cli/validator_keys/`
{% endhint %}

![](https://308419898-files.gitbook.io/\~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FdVSgPPZBTAuo92ftAAfx%2Fuploads%2FjULvvWurGZ7SUIWu42is%2Fimage.png?alt=media\&token=1a15b1b5-0c86-4c7a-93e7-407c4c285876)​

Or input the content of the `deposit_data-[timestamp].json:`\
Use `cat deposit_data-[timestamp].json` to show the content.

![](https://308419898-files.gitbook.io/\~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FdVSgPPZBTAuo92ftAAfx%2Fuploads%2FChHTgIHADOw2TMeRWRFk%2Fimage.png?alt=media\&token=4b30120a-8390-4c77-bff8-efab231e5467)

Copy the content and paste it here:

​![](https://308419898-files.gitbook.io/\~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FdVSgPPZBTAuo92ftAAfx%2Fuploads%2FY5MPNmR3VWJcb0ttacVX%2Fimage.png?alt=media\&token=dc57541f-97c6-4dc8-8d7e-072e4a4fc841)​

Click _Continue._

#### Check the summary and risks <a href="#check-the-summary-and-risks" id="check-the-summary-and-risks"></a>

​![](https://308419898-files.gitbook.io/\~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FdVSgPPZBTAuo92ftAAfx%2Fuploads%2FAAJmB8t5xS3lCVUURGL1%2Fimage.png?alt=media\&token=76c95620-9fa6-4913-ae95-9aaa07cbd3c1)​

Check the summary and risks, then click _Continue_.

#### Connect wallet and confirm deposit <a href="#connect-wallet-and-confirm-deposit" id="connect-wallet-and-confirm-deposit"></a>

​![](https://308419898-files.gitbook.io/\~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FdVSgPPZBTAuo92ftAAfx%2Fuploads%2F9gEFgYGF3GWfyHRG0bSP%2Fimage.png?alt=media\&token=11aff017-8e94-4e17-8ca0-7cfcbd1087dc)​​![](https://308419898-files.gitbook.io/\~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FdVSgPPZBTAuo92ftAAfx%2Fuploads%2FJNQGRnjDMa9wEWd9nsSv%2Fimage.png?alt=media\&token=b6ae9cac-a548-4b20-a0ea-af9a6302ff29)​

#### Check your validator <a href="#check-your-validator" id="check-your-validator"></a>

Go to [https://opsi.de/validator/overview](https://opsi.de/validator/overview) and search for your valdiator public key, index, or deposit address.
