# Created new disk using GParted

{% hint style="danger" %}
Note: Make sure you are careful when creating new partitions, as this may affect the data on the disk. Make sure you have a proper backup before continuing.
{% endhint %}

Start `parted` on the desired disk (e.g., `/dev/sda`):

```bash
sudo parted /dev/sda
```

Within `parted`, create the new partition (`sda4` in this case) and specify its size:

```bash
mkpart primary ext4 0% 100%
```

Once the command is executed successfully, exit parted:

```
quit
```

Make sure that the new partition has been created by running the following command:

```bash
sudo fdisk -l /dev/sda
```

Next, you can create a file system on the new partition (sda4) by using a command like mkfs.ext4. For example:

```
sudo mkfs.ext4 /dev/sda4
```

{% hint style="info" %}
Remember to change the file system type (ext4 in the example above) according to your needs.
{% endhint %}

