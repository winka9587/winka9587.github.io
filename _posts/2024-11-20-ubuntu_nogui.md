---
title: ubuntu启动后没有图形界面
date: 2024-11-20 16:17
category: 
author: 
tags: [修电脑]
summary: 
---

症状:

进入后可以正常输入 用户名+密码 登录, 但是没有图形界面。

runlevel运行后查看等级是5 (5是图形界面，3是文本界面)


查看

~~~
cat /etc/X11/default-desplay-manager
~~~

目前的状态是active(exited)
![](/assets/img/2024-11-20-16-17-49.png)

systemctl is-active graphical.target
返回active
说明已经启动了图形界面，但是如何进入？
我现在是在tty1

~~~
startx
~~~
命令后

![](/assets/img/2024-11-20-16-18-04.png)

退出后输出如下：

![](/assets/img/2024-11-20-16-18-14.png)


似乎也没什么问题，有没有可能是显卡驱动的问题？

重新安装显卡驱动，似乎也不是显卡驱动的问题

![](/assets/img/2024-11-20-16-18-23.png)


怀疑是gdm3的问题，但是看gdm3的日志也同样没有报错

安装lightdm代替gdm3试试，启动lightdm后, 有下面一个报错，但是可以进入到登录页面（依然无法登录）

![](/assets/img/2024-11-20-16-19-08.png)

使用https://www.reddit.com/r/Ubuntu/comments/1eu24ct/if_youre_getting_could_not_switch_the_monitor/
中的方法解决了上面出现的lightdm的报错，排除该因素后，进入到登录页面后，输入密码回车无法进入，

~~~
"failed to start session"
~~~

查看日志输出如下：
![](/assets/img/2024-11-20-16-20-36.png)

检查日志
/var/log/lightdm下的日志
~~~
Seat seat0: Failed to find session configuration ubuntu
~~~

~~~
sudo apt-get install --reinstall ubuntu-desktop
~~~

![](/assets/img/2024-11-20-16-21-09.png)


**解决方案**

尝试重新安装ubuntu-desktop

~~~
sudo apt-get install --reinstall ubuntu-desktop
~~~

中间有部分报错, 因为没有找到相关依赖, 编辑/etc/apt/sources.list添加aliyun源
~~~
deb https://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
 
deb https://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
 
deb https://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
 
# deb https://mirrors.aliyun.com/ubuntu/ jammy-proposed main restricted universe multiverse
# deb-src https://mirrors.aliyun.com/ubuntu/ jammy-proposed main restricted universe multiverse
 
deb https://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
~~~

在添加阿里源的时候，发现罪魁祸首是该文件中被意外地添加了一个**xeriol**的源

剩下的就是:
~~~
如果https报错就换为http
...
sudo apt-get update 
sudo apt-get upgrade
~~~

解决!
