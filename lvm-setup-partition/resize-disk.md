# Resize disk

{% hint style="info" %}
This partition depends on your needs, you can set how many partitions are needed.
{% endhint %}

Boot to mode rescue :&#x20;

```bash
sudo partprobe /dev/sda
```

You need to check and update system files on `/dev/sda3` partition.

```bash
sudo e2fsck -f /dev/sda3
```

After that, you can change the file system size to 30GB:

```bash
sudo resize2fs /dev/sda3 30G
```

Once done, you can reboot your system.
