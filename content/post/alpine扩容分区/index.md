---
title: alpine扩容分区
slug: alpine扩容分区
date: 2024-12-22 18:00:00+0800
tags:
    - alpine
weight: 1
---

# 安装resize2fs和growpart
```bash
apk add e2fsprogs-extra cloud-utils-growpart
```

# 运行fdisk查看要扩容的磁盘名
```bash
fdisk -l
```

# 注意这里是指的sda的第3块分区，不是sda3
```bash
growpart /dev/sda 3
```

# 重新定义sda3的ext4文件系统大小
```bash
resize2fs /dev/sda3
```
