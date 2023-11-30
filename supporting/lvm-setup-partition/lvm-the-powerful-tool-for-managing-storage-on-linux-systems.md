# LVM: The Powerful Tool for Managing Storage on Linux Systems

{% hint style="danger" %}
It is highly recommended to use a new or fresh disk condition to avoid any errors, for this setup, I'm using Hetzner.
{% endhint %}

Please order a cloud server, auction server, or dedicated server.

{% hint style="info" %}
You can use this [link](https://hetzner.cloud/?ref=fxDrSw6FUI51) to get a discount on setting up a cloud server.
{% endhint %}

Click install OS with rescue system

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

After you've completed your order, please SSH into the server using the command :&#x20;

```bash
ssh root@<IP_ADDRESS_SERVER>
```

Run this command for the OS installation. You are free to choose the OS that suits your needs.

```bash
root@rescue ~ # installimage
```

#### SWRAID <a href="#swraid" id="swraid"></a>

If the server has multiple drives, you can use the variables `SWRAID` and `SWRAIDLEVEL` to create different software RAID levels. Any software RAID levels are always applied to all drives. (That means drives marked with DRIVE, as discussed above.) drives. If you don't want software RAID on a particular drive, you'll need to remove it accordingly.

The script can create software RAID with levels 0, 1, 5, 6 or 10

```
SWRAID 1
SWRAIDLEVEL 0
```

#### Hostname <a href="#hostname" id="hostname"></a>

The variable `HOSTNAME` sets the corresponding host name in the system.

Go to the bottom and execute a command similar to the following.

```
#PART swap swap 32G
#PART /boot ext3 1024M
#PART / ext4 all
```

Go to the logical volume section and execute the command below.

```
PART /boot ext3 1024M
PART lvm    vg0  all

#LV <VG0> <name> <mount> <filesystem> <size>

LV vg0 root            /                ext4    40G
LV vg0 swap            swap             swap    4G
LV vg0 home            /home            xfs     10G
LV vg0 app_testnet     /app/testnet     ext4    30G
LV vg0 data_testnet    /data/testnet    xfs     250G
LV vg0 data_docker     /data/docker     xfs     50G
```

After completing the steps, perform a server reboot, and your Logical Volume Management (LVM) setup should be successfully created.\


Result :&#x20;

```bash
$ lsblk

NAME                   MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINTS
nvme0n1                259:0    0 476.9G  0 disk
├─nvme0n1p1            259:2    0     1G  0 part
│ └─md0                  9:0    0  1022M  0 raid1 /boot
└─nvme0n1p2            259:3    0 475.9G  0 part
  └─md1                  9:1    0 951.6G  0 raid0
    ├─vg0-root         253:0    0    40G  0 lvm   /
    ├─vg0-swap         253:1    0     4G  0 lvm   [SWAP]
    ├─vg0-home         253:2    0    10G  0 lvm   /home
    ├─vg0-app_testnet  253:3    0    30G  0 lvm   /app/testnet
    ├─vg0-data_testnet 253:4    0   500G  0 lvm   /data/testnet
    └─vg0-data_docker  253:5    0   100G  0 lvm   /data/docker
nvme1n1                259:1    0 476.9G  0 disk
├─nvme1n1p1            259:4    0     1G  0 part
│ └─md0                  9:0    0  1022M  0 raid1 /boot
└─nvme1n1p2            259:5    0 475.9G  0 part
  └─md1                  9:1    0 951.6G  0 raid0
    ├─vg0-root         253:0    0    40G  0 lvm   /
    ├─vg0-swap         253:1    0     4G  0 lvm   [SWAP]
    ├─vg0-home         253:2    0    10G  0 lvm   /home
    ├─vg0-app_testnet  253:3    0    30G  0 lvm   /app/testnet
    ├─vg0-data_testnet 253:4    0   500G  0 lvm   /data/testnet
    └─vg0-data_docker  253:5    0   100G  0 lvm   /data/docker
```
