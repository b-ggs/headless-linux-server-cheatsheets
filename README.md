# headless-linux-server-cheatsheets
🐧 istg i'm never searching for how to mount a drive w proper permissions ever again

## Table of Contents
1. [Drive Management](#drive-management)
    1. [Mounting a(n) (external) drive's partition](#mounting-an-external-drives-partition)
    2. [Unmounting a(n) (external) drive](#unmounting-an-external-drive)

---

## Drive Management

### Mounting a(n) (external) drive's partition

#### Look for partition in `lsblk`

```bash
belford@gibson:/$ lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0 29.8G  0 disk
├─sda1   8:1    0  512M  0 part /boot/efi
└─sda2   8:2    0 29.3G  0 part /
sdb      8:16   0  3.7T  0 disk
├─sdb1   8:17   0  200M  0 part
└─sdb2   8:18   0  3.7T  0 part
```

In this case, I'm using `/dev/sdb2` which is an exFAT partiton on my 4 TB external USB hard drive

#### Create a directory where you will mount your partition on

```bash
$ sudo mkdir -p /mnt/seagate
```

I'll be mounting mine on `/mnt/seagate`

#### Mount the partition and ensure that the current unprivileged user owns the files

```bash
$ sudo mount -t exfat -o uid=$USER,gid=$USER /dev/sdb2 /mnt/seagate
```

Replace `exfat` with the proper filesystem of your partition, e.g. `ext4`, etc.

#### Verify mounted volume

```bash
belford@gibson:/mnt/seagate$ ls -lah
total 7.8M
drwxr-xr-x 30 belford belford 256K Jun  3 18:47  .
drwxr-xr-x  3 root    root    4.0K Jun  3 18:34  ..
drwxr-xr-x  2 belford belford 256K Aug 13  2019 '$RECYCLE.BIN'
```

The contents of the mounted partition should now belong to the current user

```bash
belford@gibson:/mnt/seagate$ lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0 29.8G  0 disk
├─sda1   8:1    0  512M  0 part /boot/efi
└─sda2   8:2    0 29.3G  0 part /
sdb      8:16   0  3.7T  0 disk
├─sdb1   8:17   0  200M  0 part
└─sdb2   8:18   0  3.7T  0 part /mnt/seagate
```

`lsblk` also shows the mount point next to the partition

### Unmounting a(n) (external) drive

```bash
sudo umount /dev/sdb2
```
