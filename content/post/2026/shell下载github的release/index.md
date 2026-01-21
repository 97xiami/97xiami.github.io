---
title: Shell下载GitHub的Release
date: 2026-01-21 18:44:00+0800
tags:
  - linux
weight: 1
---

```bash
#!/bin/bash

# 定义仓库数组
repositories=(
  # 作者/项目名:版本tag:要下载的文件名正则表达式
  "AdguardTeam/AdGuardHome:v0.107.48:AdGuardHome_linux_amd64\.tar\.gz$"
  "VSCodium/vscodium:1.88.1.24104:VSCodiumSetup-x64-[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+\.exe$"
  # 在此添加更多仓库
)

# 文件下载位置
download_dir="/root/downloads"

# 检查依赖工具
if ! command -v curl &> /dev/null || ! command -v wget &> /dev/null; then
  echo "请安装 curl 和 wget 工具。"
  exit 1
fi

# 循环遍历每个仓库并下载
for repo_info in "${repositories[@]}"; do
  # 添加随机延时
  sleep $((RANDOM % 5 + 1))

  # 获取仓库信息
  IFS=':' read -ra repo_data <<< "$repo_info"
  repo_url="${repo_data[0]}"
  local_tag="${repo_data[1]}"
  filename="${repo_data[2]}"
  if [ -z "$local_tag" ]; then
    echo "未获取到本地版本号"
    continue
  fi

  # 获取GitHub API信息
  github_api=$(curl -s "https://api.github.com/repos/$repo_url/releases/latest")
  if [ -z "$github_api" ]; then
    github_api=$(curl -s "https://api.github.com/repos/$repo_url/releases/latest")
    if [ -z "$github_api" ]; then
      echo "无法获取GitHub API信息"
      continue
    fi
  fi

  # 获取最新版本号
  remote_tag=$(echo "$github_api" | grep -o '"tag_name": "[^"]*' | cut -d '"' -f 4)
  if [ -z "$remote_tag" ]; then
    remote_tag=$(echo "$github_api" | awk '/tag_name/{print $4;exit}' FS='[""]')
    if [ -z "$remote_tag" ]; then
      echo "未获取到版本号"
      continue
    fi
  fi

  # 打印本地和最新版本号
  echo "最新版本号 for $repo_url: $remote_tag"
  echo "本地版本号 for $repo_url: $local_tag"
