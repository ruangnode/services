# CreditCoin

<figure><img src="../../.gitbook/assets/project3 (2).jpg" alt="" width="100"><figcaption></figcaption></figure>

## Minimum Requirements

The most common way for a beginner to run a validator is on a cloud server running Linux. We strongly recommend using a recent Debian Linux OS. For this guide we will be using **Ubuntu 22.04**, but the instructions should be similar for other platforms.Creditcoin does not currently support Macs using Apple silicon, such as the M1 and M2The transaction weights in Creditcoin are benchmarked on reference hardware. We ran the benchmark on a memory optimized `Standard_E4as_v4` VM instance of Azure.

* **CPU**
  * Intel Core i5-8400 or better
    * 6 Cores @ 2.8Ghz
    * 9M Cache
* **Storage**
  * 64 GB
* **Memory**
  * 8GB
* **System**
  * Ubuntu 22.04 (Linux Kernel 5.16 or newer)

{% hint style="info" %}
It is strongly recommended that validators either meet or exceed these hardware specifications in order to ensure that they can process all blocks in time. If you use subpar hardware you will possibly run into performance issues, get less era points, and potentially even get slashed.
{% endhint %}
