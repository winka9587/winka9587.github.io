---
title: ubuntu 相关的工具/命令 合集
date: 2024-11-20 16:04
category: 
author: 
tags: []
summary: 
---

汇总ubuntu上使用的一些相关工具/命令

#### ubuntu录制视频转换

ubuntu自带的录制工具输出格式为webm, windows上播放会有卡顿等问题.

ffmpeg的转换命令

~~~
# 固定帧率 (30fps)
ffmpeg.exe -i D:/input.webm -r 30 -c:v libx264 -preset slow -crf 22 -c:a aac -b:a 128k output.mp4

# 保持原帧率
ffmpeg.exe -i D:/input.webm -c:v libx264 -preset slow -crf 22 -vsync 2 -c:a aac -b:a 128k output.mp4
~~~

### Server相关

#### github密钥相关

##### 无法连接

首先需要排除代理的问题

网页可以登录github
http.proxy和https.proxy已经按照系统代理的端口进行了设置
但是在终端
![](/assets/img/2024-11-20-16-13-05.png)

测试连接

![](/assets/img/2024-11-20-16-13-11.png)

测试443端口

![](/assets/img/2024-11-20-16-13-14.png)

如果是第一次连接是上面的情况，输入yes之后再连接是下面

说明443端口没问题，但是22端口有问题


找到git目录，修改xxx/Git/etc/ssh/ssh_config
在最底下加上
注意加上密钥的位置
Host github.com
    User git
    Hostname ssh.github.com
    PreferredAuthentications publickey
    IdentityFile C:/Users/Administrator/.ssh/id_rsa
    Port 443
    
设置完保存。重启终端测试

![](/assets/img/2024-11-20-16-11-44.png)

##### ssh密钥的生成
https://blog.csdn.net/Serena_tz/article/details/115109206

或者最简单的:


生成密钥
~~~
ubuntu
https://help.ubuntu.com/community/SSH/OpenSSH/Keys
win
https://learn.microsoft.com/zh-cn/windows-server/administration/openssh/openssh_keymanagement
ssh-keygen -t rsa -b 2048 -f /path/remote-ssh-auth
~~~

-f 是指定保存路径和名字, 如果仅有一个名字的话是会在当前路径下生成

![](/assets/img/2024-11-20-16-14-59.png)

生成了一个公钥(.pub)一个私钥

可以通过-f 指定路径例如

~~~
ssh-keygen -t rsa -b 2048 -f D:/dddxxx/xx/keyname
~~~



一般来说，windows默认生成的密钥在目录下
C:\Users\{username}\.ssh

如果找不到可以使用
ssh-keygen
看看保存到了哪里

##### Mac遇到的问题

~~~
ssh -T git@github.com
~~~

显示

~~~
git@github.com: Permission denied (publickey).
~~~

**解决方案：**

是因为在生成密钥的时候，并非使用默认的密钥名字，而是使用-f 参数手动设置的
需要在本地手动添加

~~~
ssh-add ~/.ssh/{自定义名字}
~~~

成功, 输出如下:
~~~
ssh -T git@github.com             
Hi winka9587! You've successfully authenticated, but GitHub does not provide shell access.
~~~

#### 同一个账户不同目录使用不同github密钥

一台机器多人使用:

使用git config自带的includeIf
https://wiki.eryajf.net/pages/6ec567/#%E7%9B%B8%E5%90%8C%E6%89%98%E7%AE%A1%E7%AB%99

首先解除原本的global设置，如果PC仅一个人使用，使用全局设置无可厚非。但现在情况不同，如果没有接触，会自动使用默认的全局配置。

包含两部分
1.git config --global user.name
user.email
使用unset解除
2.~/.ssh/下的密钥，删除github-setting中对应的即可
如果只做了操作1，那么当一个用户随便设置一个global的user.name和user.email即可使用该密钥，以该密钥绑定的github用户的身份进行操作。

#### git添加huggingface密钥

https://huggingface.co/docs/hub/en/security-git-ssh#generating-a-new-ssh-keypair

在这之后，如果要访问hf，还需要access token（好像直接进行这一步可以跳过上面的ssh密钥部分，下次测试）
https://huggingface.co/docs/huggingface_hub/en/guides/cli#huggingface-cli-login
![](/assets/img/2024-11-20-16-08-06.png)


**Max retries exceeded with url ...**
![](/assets/img/2024-11-20-16-08-13.png)
输入token后一段时间超时，挂梯子解决。

#### s