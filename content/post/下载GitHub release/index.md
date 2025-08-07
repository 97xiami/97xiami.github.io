---
title: Shell下载GitHub的Release
slug: Shell下载GitHub的Release
date: 2024-12-30 13:00:00+0800
tags:
  - Shell
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

  if [ "$local_tag" == "$remote_tag" ]; then
    echo "无更新 for $repo_url"
  else
    # 删除旧版本
    find $download_dir -type f -regex "$download_dir\/$filename" -exec rm -f {} \;
    # 获取下载链接
    download_url=$(echo "$github_api" | grep -o '"browser_download_url": "https://github.com[^"]*' | cut -d '"' -f 4 | grep -E "$filename")
    if [ -z "$download_url" ]; then
      echo "无法获取下载链接"
      continue
    fi

    filename=$(basename "$download_url")
    # 使用curl下载
    sleep $((RANDOM % 5 + 1)) # 添加随机延时
    if curl -sLo "$download_dir/$filename" "$download_url"; then
      echo "已下载最新版文件 for $repo_url: $filename"
      # 下载完成后替换本文件版本号
      sed -i "s,${repo_url//\//\\/}:${local_tag//\//\\/},${repo_url//\//\\/}:${remote_tag//\//\\/}," "$0"
    else
      echo "curl下载失败，转为wget下载"
      # 使用wget下载
      sleep $((RANDOM % 5 + 1)) # 添加随机延时
      if wget -P "$download_dir" "$download_url"; then
        echo "已下载最新版文件 for $repo_url: $filename"
        # 下载完成后替换本文件版本号
        sed -i "s,${repo_url//\//\\/}:${local_tag//\//\\/},${repo_url//\//\\/}:${remote_tag//\//\\/}," "$0"
      else
        echo "wget下载失败"
      fi
    fi
  fi
done
```
