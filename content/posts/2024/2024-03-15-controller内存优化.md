---
title: "kubernetes controller 内存优化"
date: 2024-03-15T15:48:00+08:00
draft: true
toc: true
images:
tags:
  - golang
  - kubernetes
  - controller
---

0x01 背景
近期在sealos集群排查资源占用情况中,发现了我们自己的部分controller在最大的集群上出现了内存使用较高的情况,同时在其他的集群并没有发现特别的资源占用异常
镜像版本和配置参数都是一致的情况下,
会因为集群下其他负载数量
