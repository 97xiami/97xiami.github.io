---
title: Docker使用Nftable
date: 2024-12-28 17:00:00+0800
tags: Docker
weight: 1
---

## 配置`/etc/docker/daemon.json`禁用iptables

`{`

`    "iptables": false`

`}`

## 自定义docker虚拟网卡

>

> 自行修改为喜欢的ip段和网卡名称，

> \`mynet\`是docker容器使用时用的名称，

> \`user0\`是宿主机执行\`ip addr\`显示的网卡名称，

> 因为容器默认创建的网卡名都是\`br-xxxxxxxx\`，所以要指定名称

\`\`\`bash

docker network create mynet --driver bridge --subnet 172.10.0.0/16 --gateway 172.10.0.1 -o com.docker.network.bridge.name=user0

\`\`\`

> docker-compose.yml配置

\`\`\`yml

services:

  nginx:

    image: nginx

    networks:

      mynet:

        ipv4_address: 172.10.0.2

networks:

  mynet:

    external: true

\`\`\`

## nftables配置

\`\`\`nftables

#!/usr/bin/nft -f

flush ruleset

# 默认网卡docker0的IP段，一般情况不用修改

define docker0_ip = 172.17.0.0/16

# 自定义的网卡名称及IP段

define docker_if = "user0"

define user0_ip = 172.10.0.0/16

table ip nat {

  chain PREROUTING {

    type nat hook prerouting priority dstnat; policy accept;

    # 目标地址为本地地址，则转发到DOCKER链表

    fib daddr type local jump DOCKER

  }

  chain OUTPUT {

    type nat hook output priority dstnat; policy accept;

    # 目标地址为本地地址且不在127.0.0.0/8中，则转发到DOCKER链表

    ip daddr != 127.0.0.0/8 fib daddr type local jump DOCKER

  }

  chain POSTROUTING {

    type nat hook postrouting priority srcnat; policy accept;

    # 伪装地址使容器能够访问外部网络

    oifname != "docker0" ip saddr $docker0_ip masquerade

    oifname != $docker_if ip saddr $user0_ip masquerade

    # 伪装容器内部通讯时的地址

    ip saddr $docker0_ip ip daddr $docker0_ip masquerade

    ip saddr $user0_ip ip daddr $user0_ip masquerade

  }

  chain DOCKER {

    # 放行docker网卡

    iifname "docker0" return

    iifname $docker_if return

    # 设置容器端口转发

    iifname != $docker_if tcp dport 80 dnat to 172.10.0.2:8080

  }

}

table ip filter {

  chain INPUT {

    type filter hook input priority filter; policy drop;

    # 放行lo网卡

    iifname "lo" accept

    # 放行默认docker0和自定义虚拟网卡

    iifname "docker0" accept

    iifname $docker_if accept

    # 放行已建立连接和已关联的连接

    ct state established, related accept

    # 放行icmp请求，限速每秒100个包

    ip protocol icmp icmp type { destination-unreachable, time-exceeded, parameter-problem } limit rate 100/second accept

  }

  chain FORWARD {

    type filter hook forward priority filter; policy drop;

    # 放行docker网卡

    oifname "docker0" ct state established,related accept

    oifname "docker0" iifname != "docker0" ip daddr $docker0_ip accept

    iifname "docker0" accept

    # 放行自定义docker虚拟网卡

    oifname $docker_if ct state established,related accept

    oifname $docker_if iifname != $docker_if ip daddr $user0_ip accept

    iifname $docker_if accept

  }

}

\`\`\`

> 有需要可以将规则设置为更加详细的指定端口放行

\`\`\`nftables

chain POSTROUTING {

  ...

  ip saddr 172.10.0.2 ip daddr 172.10.0.2 tcp dport 8080 masquerade

  ...

}

chain FORWARD {

  ...

  oifname $docker_if iifname != $docker_if ip daddr 172.10.0.2 tcp dport 8080 accept

  ...

}

\`\`\`
