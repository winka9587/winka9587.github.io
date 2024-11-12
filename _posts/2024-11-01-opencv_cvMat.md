---
title: OpenCV 
date: 2024-11-01 20:21
category: 
author: 
tags: []
summary: 
---

两个环境下, 一个可以正常运行, 另一个却始终报错: 

~~~
undefined reference to "cv::Mat::Mat()"
~~~

使用
~~~
ldd xx.so 
~~~
查看所有使用的库, 两个环境链接的库都是libopencv_core.so.4.5

但是使用
~~~
nm -C -D /path/to/libopencv_core.so.4.5 | grep cv::Mat::Mat
~~~
结果却完全不同

![](/assets/img/2024-11-01-20-24-46.png)

![](/assets/img/2024-11-01-20-27-41.png)

结果，检查了第一个环境中的opencv应该是4.5.5, 第二个是4.5.0

