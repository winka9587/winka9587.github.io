---
title: so动态链接库 OpenCV cv::Mat::Mat() 报错
date: 2024-11-01 20:21
category: 
author: 
tags: []
summary: 
---

**排查过程浪费时间巨长, 记录避免以后再遇到**

## 背景

与其他单位合作的项目, 对方交付的.so文件, 在两台设备的环境下, 一台下可以正常运行, 另一台却始终报错: 

~~~
undefined reference to "cv::Mat::Mat()"
~~~

问题: **对方声称使用了指定的opencv版本, 如何验证？**

使用
~~~
ldd xx.so 
~~~

查看所有使用的库, 两个设备下环境链接的库都是libopencv_core.so.4.5

但是使用
~~~
nm -C -D /path/to/libopencv_core.so.4.5 | grep cv::Mat::Mat
~~~

在结果中可以发现输出不同

![](/assets/img/2024-11-01-20-24-46.png)

![](/assets/img/2024-11-01-20-27-41.png)

## 结果

检查了第一个环境中的opencv应该是4.5.5, 第二个是4.5.0

