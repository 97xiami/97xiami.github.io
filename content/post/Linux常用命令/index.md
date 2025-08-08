---
title: Linux常用命令
slug: Linux常用命令
date: 2024-12-29 17:00:00+0800
tags:
    - Linux
weight: 1
---
- [vim](#vim)

## awk

```bash
# 用`:`分割并打印`filename`第二列和行号
# `$0`表示整行，`$1`表示第一列
# `-F`指定分割符，默认空格
# `[,;]`指定分割符为`,`或`;`
awk -F ':' '{print NR, $2}' filename

# 打印第二列为`100`的行
# 支持`>`,`>=`,`==`,`<=`,'<','!='
awk '$2 == 100' filename

# 正则表达式打印包含`text`行,`!~`不包含
awk '$1 ~ /text/ {print $0}' filename

# 打印第一列等于 "text" 且第二列大于 20 的行
# `||`或者
awk '$1 == "text" && $2 > 100 {print $0}' filename

# 打印第一列不是`text`的行
awk '!($1 == "apple") {print $0}' filename
```

## du

```bash
# 只显示`test`文件夹大小，不会打印test的所有子文件
du -sh test
# `-a`显示每个文件大小，`--max-depth=1`只显示一级目录
```

## grep

```bash
# `-o`表示只显示匹配内容
grep -o
```

## journalctl

```bash
# 获取`sshd`两天内的日志
journalctl --since "2 day ago" -u sshd
```

## sed

```bash
# 打印`filename`的第一行
sed -n "1p" filename

# 删除`text.txt`中的空白行
sed -i '/^$/d' filename

# 在第三行前插入`text`
sed -i '3i\text' filename

# 在第三行后插入`text`
sed -i '3a\text' filename

# 将第三行替换为`text`
sed -i '3c\text' filename
```

## sort

```bash
# 默认按字母顺序abcde排序
# `-n`按数字12345排序
# `-r`倒序
# `-f`忽略大小写
# `-u`去除相同行
sort filename

# 合并`filename`中内容相同的行，输出为`filename`
sort -u filename -o filename

# `ip.txt`每行一个IP地址，下面的命令可以将IP地址按从小到大排序
# `-t`指定分割符
# `-k1`表示分割后第一列，`1n`表示从第一个列开始数字排序
sort -u ip.txt -o ip.txt -t '.' -k1,1n -k2,2n -k3,3n -k4,4n
```

## ss

```bash
# 查看`22`端口状态
ss -tulpn | grep 22
# `-t`表示tcp，`-u`表示udp，`-l`显示所有，`-p`显示进程和pid，`-n`直接显示ip和端口
```

## tar

```bash
# `gzip`用`-z`，`bzip2`用`-j`
# 压缩`test`目录
tar -cvf test.tar test

# 解压到`test`目录下
tar -xvf test.tar -C test

# 查看`test.tar`
tar -tvf test.tar

# 将`filename`添加到`test.tar`
tar -rvf test.tar filename

# 删除`test.tar`中的`filename`
tar --delete -f test.tar filename
```

## vim

> 在指定列插入文本，比如`#`注释
> 选定列位置后按`Ctrl + v`，然后上下移动光标选定要插入的行
> 按`Shift + i`，然后输入要插入的文本之后按`Esc`

```bash
# 将所有`0 123`替换为`4 5 6`
# `%`表示查找所有行，`g`表示替换所有匹配，`\s`表示匹配空格，替换几个空格就输入几个空格
:%s/0\s123\s/4 5 6/g

# 搜索后，`n`下一个，`N`上一个

# 文件路径不存在时创建文件夹
:!mkdir -p %:h
```
