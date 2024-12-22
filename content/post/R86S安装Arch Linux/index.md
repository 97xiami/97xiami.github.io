---
title: R86S安装Arch Linux
slug: R86S安装Arch Linux
date: 2024-12-22 14:57:00+0800
categories:
    - 教程
tags:
    - Arch Linux
weight: 1       
---

# 禁用reflector
```bash
systemctl stop reflector
```

# 配置中科大镜像源
```bash
echo "Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch" > /etc/pacman.d/mirrorlist
```

# 同步时间
```bash
timedatectl set-ntp true      #开启ntp时间同步
timedatectl set-timezone PRC  #设置时区为中国
timedatectl status            #查看时间
```

# 转换磁盘类型为GPT
```bash
lsblk  #显示分区信息
parted /dev/nvme0n1   #执行parted，进行分区操作
(parted)mktable       #输入mktable
New disk lebel type?  #输入gpt，如果有数据会有Warining，输入yes
(parted)quit          #输入quit退出
```

# 分区
```bash
cfdisk /dev/nvme0n1  #使用cfdisk图形化分区
#EFI分区的Size Type选择EFI System，大小1G就行
#其他分区用默认的Linux filesystem
#分区完成后选择Write回车，输入yes写入分区信息；选择Quit退出
```

# 格式化分区
```bash
mkfs.vfat /dev/nvme0n1p1  #格式化EFI分区
mkfs.ext4 /dev/nvme0n1p2  #格式化其他分区为EXT4格式
```

# 挂载分区
```bash
mount /dev/nvme0n1p2 /mnt      #先挂载根分区
mkdir /mnt/efi                 #创建efi目录
mount /dev/nvme0n1p1 /mnt/efi  #挂载efi分区
```

# 安装基础包
```bash
pacstrap /mnt base linux-lts linux-lts-headers linux-firmware dhcpcd vim bash-completion #这里使用lts内核，有无线网卡加上iwd
```
