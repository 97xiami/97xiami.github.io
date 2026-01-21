---
title: ttyd网页shell
date: 2026-01-21 18:44:00+0800
tags:
  - linux
  - ttyd
weight: 1
---

## 自行安装 ttyd

[https://github.com/tsl0922/ttyd](https://github.com/tsl0922/ttyd)

## 添加 systemd 服务`/etc/systemd/system/ttyd.service`

```service
[Unit]
Description=TTYD
After=syslog.target
After=network.target

[Service]
ExecStart=/usr/bin/ttyd -W -p 2222 -c admin:password bash
Type=simple
Restart=always
User=root
Group=root

[Install]
WantedBy=multi-user.target
```

## 执行命令

```bash
systemctl daemon-reload
systemctl enable ttyd
systemctl start ttyd
```
