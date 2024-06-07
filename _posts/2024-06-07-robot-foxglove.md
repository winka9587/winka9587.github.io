---
layout: post
title: 
date: 2024-06-07 10:33
category: 
author: 
tags: []
summary: 使用foxglove传输并可视化robot数据
---

一开始尝试参考着:

https://foxglove.dev/blog/installing-ros1-on-macos-with-docker

来使用docker镜像

但是运行到

~~~
docker pull ghcr.io/foxglove/noetic-ros1-bridge:latest
~~~

这一步就开始出错, 经过不懈寻找, 找到这个2023.2月的内容:

https://github.com/foxglove/ros-foxglove-bridge/issues/150

无奈, 自己编译吧

https://github.com/foxglove/ros-foxglove-bridge

