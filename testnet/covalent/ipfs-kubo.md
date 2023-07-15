# Ipfs Kubo

**Requierment Linux x86\_64 (Ubuntu 22.04 LTS)**

```
wget https://dist.ipfs.tech/go-ipfs/v0.18.0/go-ipfs_v0.18.0_linux-amd64.tar.gz
tar -xzvf go-ipfs_v0.18.0_linux-amd64.tar.gz
cd go-ipfs
bash install.sh
ipfs init
```

{% hint style="info" %}
You can see ipfs version here [https://dist.ipfs.tech/go-ipfs/versions](https://dist.ipfs.tech/go-ipfs/versions)
{% endhint %}

```
sudo chmod -R 700 ~/.ipfs
ipfs config profile apply server
```

<pre><code>go to directory .ipfs

modify file <a data-footnote-ref href="#user-content-fn-1">config</a>

Discovery.MDNS.Enabled = false, Swarm.DisableNatPortMap = true
</code></pre>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8).png" alt="" width="326"><figcaption></figcaption></figure>

{% hint style="info" %}
After modify file config please `apply profile server`
{% endhint %}

[^1]: 
