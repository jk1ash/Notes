## 1. 新建磁盘分区

```shell
fdisk -l #查看所有磁盘

fdisk /dev/sdb #新建磁盘分区
```

## 2. 格式化硬盘

可选格式ext4、ext3、ext2等

```shell
mkfs -t ext4 /dev/sdb1
```

## 3. 挂载到系统中

```shell
mkdir /extra-storage

mount -t ext4 /dev/sdb1 /extra-storage

df -lh #查看是否挂载成功
```

## 4. 开机自动挂载

```shell
vim /etc/fstab
```

输入

```shell
/dev/sdb1 /extra-storage ext4 defaults 0 0
```

## 5. 查看磁盘的uuid

```shell
ls -al /dev/disk/by-uuid/*

blkid /dev/sdb1
```

## 6. 修复磁盘文件系统

```shell
fsck /dev/sdb1
```
