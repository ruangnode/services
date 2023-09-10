# LVM: The Powerful Tool for Managing Storage on Linux Systems

{% hint style="danger" %}
It is highly recommended to use a new or fresh disk condition to avoid any errors.
{% endhint %}

Check your patition disk, follow this command `lsblk` :

```
NAME           MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda              8:0    0   400G  0 disk
├─sda1           8:1    0     1M  0 part
├─sda2           8:2    0     2G  0 part /boot
├─sda3           8:3    0   398G  0 part /
```

{% hint style="info" %}
If the partition condition is like this, you should unmount the sda3 partition in order to create a new disk, sda4, as shown in the image below.
{% endhint %}

Example partition :&#x20;

```
NAME           MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda              8:0    0   400G  0 disk
├─sda1           8:1    0     1M  0 part
├─sda2           8:2    0     2G  0 part /boot
├─sda3           8:3    0  25.9G  0 part /
└─sda4           8:4    0 372.1G  0 part
```

{% hint style="info" %}
To perform the partitioning mentioned above, you need to use rescue mode provided by your service provider. For Hetzner users, you can perform the partitioning during the initial setup installation. For partitioning reference for Hetzner users, you can visit this [link](https://www.youtube.com/watch?v=dC7eUi0D8DM\&t=195s)
{% endhint %}

