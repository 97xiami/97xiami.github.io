---
title: PVE删除local-lvm，并合并到local
slug: PVE删除local-lvm，并合并到local
date: 2024-12-22 17:57:00+0800
categories:
    - 教程
tags:
    - PVE
---

# 删除local-lvm并合并到local
```bash
lvremove pve/data
lvextend -l+100%FREE /dev/mapper/pve-root
resize2fs /dev/mapper/pve-root
```
