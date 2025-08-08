---
title: 中兴U30air/F50教程
slug: 中兴U30air/F50教程
date: 2025-04-29 10:00:00+0800
tags:
  - 中兴U30air
  - 中兴F50
weight: 1
---

## 性能模式

访问[http://192.168.0.1](https://192.168.0.1)，登录后访问[http://192.168.0.1/index.html#performance_mode](http://192.168.0.1/index.html#performance_mode)

## adb 调试

访问[http://192.168.0.1](https://192.168.0.1)，登录后访问[http://192.168.0.1/index.html#usb_port](http://192.168.0.1/index.html#usb_port)

## 下载[Escrcpy](https://github.com/viarotel-org/escrcpy)

## 拨号输入`*#*#83781#*#*`，进入工程模式，切换到`DEBUG&LOG`，点击`Send AT Command`，在`AT Command`输入框中输入命令后点击`SEND`

> Channel 的 0 是卡一，1 是卡二
> AT+SPIMEI=0 的 0 是卡一，1 是卡二

```echo
查询sim卡签约速率：AT+CGEQOSRDP=1
修改串号：AT+SPIMEI=0,"要修改的IMEI"
查询串号：AT+SPIMEI?
```
