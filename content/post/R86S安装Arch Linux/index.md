---
title: R86S安装Arch Linux
slug: R86S安装Arch Linux
date: 2024-12-22 14:57:00+0800
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
timedatectl set-ntp true  # 开启ntp时间同步
timedatectl set-timezone PRC  # 设置时区为中国
timedatectl status  # 查看时间
```

# 转换磁盘类型为GPT
```bash
lsblk  # 显示分区信息
parted /dev/nvme0n1  # 执行parted，进行分区操作
(parted)mktable  # 输入mktable
(parted)New disk lebel type?  # 输入gpt，如果有数据会有Warining，输入yes
(parted)quit  # 输入quit退出
```

# 分区
```bash
cfdisk /dev/nvme0n1  # 使用cfdisk图形化分区
# EFI分区的Size Type选择EFI System，大小1G就行
# 其他分区用默认的Linux filesystem
# 分区完成后选择Write回车，输入yes写入分区信息；选择Quit退出
```

# 格式化分区
```bash
mkfs.vfat /dev/nvme0n1p1  # 格式化EFI分区
mkfs.ext4 /dev/nvme0n1p2  # 格式化其他分区为EXT4格式
```

# 挂载分区
```bash
mount /dev/nvme0n1p2 /mnt  # 先挂载根分区
mkdir /mnt/efi  # 创建efi目录
mount /dev/nvme0n1p1 /mnt/efi  # 挂载efi分区
```

# 安装基础包
```bash
pacstrap /mnt base linux-lts linux-lts-headers linux-firmware dhcpcd vim bash-completion # 这里使用lts内核，有无线网卡加上iwd
```

# 生成fstab文件
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

# chroot进/mnt
```bash
arch-chroot /mnt
```

# 设置时区并写入硬件
```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime  # 创建Shanghai时区的软链接
hwclock --systohc  # 时间信息写入硬件
```

# 设置local本地化
```bash
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
echo "zh_CN.UTF-8 UTF-8" >> /etc/locale.gen
locale-gen
echo 'LANG=en_US.UTF-8'  > /etc/locale.conf
```

# 设置主机名和hosts
```bash
echo "arch" > /etc/hostname
echo "127.0.0.1  localhost" >> /etc/hosts
echo "::1        localhost" >> /etc/hosts
echo "127.0.1.1  arch" >> /etc/hosts
```

# 设置root密码
```bash
passwd root
```

# 安装CPU微码
```bash
pacman -S intel-ucode
```

# 安装引导程序
```bash
pacman -S grub efibootmgr  # grub是启动引导器，efibootmgr被 grub 脚本用来将启动项写入 NVRAM。
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB  # 配置grub信息
sed -i "s/loglevel=3 quiet/loglevel=5 nowatchdog quiet/g" /etc/default/grub  # loglevel改为5是为了更方便排错，nowatchdog可以加快开关机速度
grub-mkconfig -o /boot/grub/grub.cfg  # 将grub配置写入
```

# 安装完成，重启
```bash
exit  # 退回安装环境
umount -R /mnt  # 卸载新分区
reboot  # 重启
```

# 重启后启动dhcpcd联网
```bash
systemctl enable dhcpcd
ststemctl start dhcpcd
```

# 配置swapfile
```bash
dd if=/dev/zero of=/swapfile bs=1M count=4096 status=progress  # 创建4G的交换空间 大小根据需要自定
chmod 600 /swapfile # 设置正确的权限
mkswap /swapfile # 格式化swap文件
swapon /swapfile # 启用swap文件
echo "/swapfile none swap defaults 0 0" >> /etc/fstab  # 将swapfile写入fstab开机自动挂载
```

# 安装Intel集显驱动
```bash
pacman -S mesa vulkan-intel
```

# 安装smartmontools查看硬盘信息
```bash
pacman -S smartmontools
```
