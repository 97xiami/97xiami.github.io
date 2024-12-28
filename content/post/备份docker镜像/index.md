---
title: 备份Docker镜像
slug: 备份Docker镜像
date: 2024-12-28 18:00:00+0800
tags:
    - Docker
weight: 1
---

# 备份本机所有Docker镜像
```bash
#!/bin/bash

IMAGES=$(docker images --format "{{.Repository}}:{{.Tag}}")
for IMAGE in $IMAGES; do
  IMAGE_NAME=$(echo "$IMAGE" | sed 's/:/_/g' | sed 's/\//-/g')
  docker save -o "${IMAGE_NAME}.tar" "$IMAGE"
done
```

# 还原当前目录下所有Docker镜像
```bash
#!/bin/bash

IMAGES=$(ls *.tar)
for IMAGE in $IMAGES; do
  docker load -i $IMAGE
done
```
