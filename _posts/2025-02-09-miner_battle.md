---
title: 挖矿病毒记录
date: 2025-02-09 15:20
category: 
author: winka9587
tags: []
summary: 
image:
    path: /assets/img/2025-02-09-15-26-18.png
---

该攻击一共出现了3次, 最早一次是在23年5月份, 但是那次被托管中心的安全发现，发了警告并ban掉了整台服务器的IP访问。后面两次发生在24年1月31与2月5日，均没有被安全中心发现。

原因是因为早期服务器root权限管理混乱以及部分用户密码过弱导致。

# 2024.5

和该文中
https://blog.csdn.net/weixin_43693967/article/details/130753155

是同一种


找到启动的文件（二进制文件伪装名为system）后, 尝试使用

~~~bash
> ./system stop
~~~

![](/assets/img/2025-02-09-15-26-18.png)

同目录下可以找到其配置文件config.ini

![](/assets/img/2025-02-09-15-31-49.png)

最简答的解决方案是直接将其配置文件破坏掉，再去将其剩余的试图分配显卡资源的进程kill掉。

![](/assets/img/2025-02-09-15-33-33.png)

观察一段时间后没有重新启动。以为这件事就这么结束了。后续是回收了所有一般用户的管理员权限并更换了更严格的密码。

# 2024.1.31 

**该解决方案仅为按时间顺序的记录，彻底解决请参考最后2章节**

大过年突然发现他卷土重来，显卡占用率均100%拉满。

找到其启动位置

~~~bash
> sudo readlink /proc/<进程号>/exe
/tmp/fileRwy8XM
~~~

stat检查，确实是不久之前（root权限）创建的。

![](/assets/img/2025-02-09-15-38-08.png)

先检查一下, 确认和上一次是同一个

![](/assets/img/2025-02-09-15-39-47.png)

![](/assets/img/2025-02-09-15-40-42.png)

于是依然按照上次的操作重新进行了一遍，想等过两天去机房直接重新安装系统。但是修改后的密码测试过，其他机器暴力扫应该是破不了的。很奇怪它是如何获得root权限的。

## colmap错误

~~~
I20250602 03:47:01.171092 889386 misc.cc:198] 
==============================================================================
Exhaustive feature matching
==============================================================================
I20250602 03:47:01.181533 889386 feature_matching.cc:231] Matching block [1/1, 1/1]
I20250602 03:47:01.182898 889386 feature_matching.cc:46]  in 0.001s
I20250602 03:47:01.183050 889386 timer.cc:91] Elapsed time: 0.000 [minutes]
I20250602 03:47:01.189850 889579 misc.cc:198] 
==============================================================================
Loading database
==============================================================================
I20250602 03:47:01.192241 889579 database_cache.cc:54] Loading cameras...
I20250602 03:47:01.192351 889579 database_cache.cc:64]  9 in 0.000s
I20250602 03:47:01.192389 889579 database_cache.cc:72] Loading matches...
I20250602 03:47:01.192426 889579 database_cache.cc:78]  0 in 0.000s
I20250602 03:47:01.192451 889579 database_cache.cc:94] Loading images...
I20250602 03:47:01.192559 889579 database_cache.cc:143]  9 in 0.000s (connected 0)
I20250602 03:47:01.192592 889579 database_cache.cc:154] Building correspondence graph...
I20250602 03:47:01.192610 889579 database_cache.cc:190]  in 0.000s (ignored 0)
I20250602 03:47:01.192636 889579 timer.cc:91] Elapsed time: 0.000 [minutes]
W20250602 03:47:01.192656 889579 incremental_mapper.cc:349] No images with matches found in the database
Ran RANSAC in 0.0213 sec
 67%|██████▋   | 2/3 [00:39<00:19, 19.76s/it]
Reconstruction done in  1.0085 sec
{}
images: 9
two_view_geometries: 36
Dataset "ETs" -> Registered 0 / 9 images with 0 clusters
Inference on cluster 1 with 10 images
Shortlisting. Number of pairs to match: 45. Done in 1.1754 sec
SuperPoint: 100%|██████████| 10/10 [00:00<00:00, 12.65it/s]
Features detected in 1.0524 sec
  RoMa 10/45 pairs done
  RoMa 20/45 pairs done
  RoMa 30/45 pairs done
  RoMa 40/45 pairs done
  RoMa 45/45 pairs done
  NMS  10/10 images merged
  Pair 10/45 processed
  Pair 20/45 processed
  Pair 30/45 processed
  Pair 40/45 processed
  Pair 45/45 processed

=== 统计汇总 (mean / median) ===
KD 命中    : 2048.0 / 2048.0
最终 Kept  : 1.0 / 1.0

✅ Done. keypoints=10 imgs, matches=45 pairs
Features matched in 43.0580 sec
~~~

# 2024.2.5

当以为一切都ok的时候，突然发现它又来了，而且这次如果使用上次的方法，会立刻重新创建一个二进制文件。
例如我刚刚删除了/tmp/file1，它会立刻创建一个/tmp/file3来运行。

寻找所有使用/tmp路径的进程

~~~bash
> sudo lsof +D /tmp
~~~

发现异常, 来自一个已经废弃的账号(下图中打码的)的进程。

![](/assets/img/2025-02-09-15-47-54.png)

该账号早期有管理员权限，但是已经被废弃了。检查/tmp/.ICE-unix/路径。对比另一台正常的机器，一般来说，这下面只会有缓存目录。但是却包含了很多异常的文件：

![](/assets/img/2025-02-09-15-50-33.png)

检查config.json，包含了cpu和gpu的相关配置，基本可以确定与挖矿有关。

~~~
{
    "api": {
        "id": null,
        "worker-id": null
    },
    "http": {
        "enabled": false,
        "host": "127.0.0.1",
        "port": 0,
        "access-token": null,
        "restricted": true
    },
    "autosave": true,
    "background": false,
    "colors": false,
    "title": true,
    "randomx": {
        "init": -1,
        "init-avx2": -1,
        "mode": "auto",
        "1gb-pages": false,
        "rdmsr": true,
        "wrmsr": true,
        "cache_qos": false,
        "numa": true,
        "scratchpad_prefetch_mode": 1
    },
    "cpu": {
        "enabled": true,
        "huge-pages": true,
        "huge-pages-jit": false,
        ...
    },
    ...
}
~~~

那它到底是怎么来的呢？

## 核心功能被篡改

检查定时任务

~~~bash
> ls -lah /etc/cron.*
~~~

![](/assets/img/2025-02-09-15-44-35.png)

发现了异常

![](/assets/img/2025-02-09-15-45-09.png)

检查是一个空文件, 但是根据其Change和Access时间，应该与最近的两次攻击都有关系。

![](/assets/img/2025-02-09-15-45-35.png)

先删除该任务吧

~~~bash
> sudo rm -f /etc/cron.hourly/0
rm: cannot remove '/etc/cron.hourly/0': Operation not permitted
~~~

发现无法删除它, 没有权限。使用lsattr和chattr也无法修改器权限设置。

突然想了一下，检查下chattr

~~~bash
> ls -lah /bin/chattr
-rwxr-xr-x 1 root root 73   6  2024 /bin/chattr

> stat /bin/chattr
  File: /bin/chattr
  Size: 73         Blocks: 8          IO Block: 4096   regular file
Device: fd00h/64768d Inode: 2814158     Links: 1
Access: (0755/-rwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2025-02-05 16:47:04.362367974 +0000
Modify: 2024-02-06 19:33:23.323953572 +0000
Change: 2024-02-06 19:33:23.327953586 +0000
~~~

发现时间点与最新一次攻击非常接近。

然后去另一台正常的机器上，检查lsattr

![](/assets/img/2025-02-09-15-59-03.png)

对比发现，明显不对，这台机器上的chattr小的离谱了。正常的机器上Size为14656, 但是它只有73。一身冷汗，赶紧检查一下其他的几个常用命令：

~~~bash
> stat /bin/ls
  File: /bin/ls
  Size: 150551     Blocks: 296        IO Block: 4096   regular file
Device: fd00h/64768d Inode: 2753280     Links: 1
Access: (0755/-rwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2025-02-06 03:28:16.422220307 +0000
Modify: 2025-01-31 16:10:20.593860153 +0000
Change: 2025-01-31 16:10:20.593860153 +0000
 Birth: -

> stat /bin/ps
  File: /bin/ps
  Size: 137688     Blocks: 272        IO Block: 4096   regular file
Device: fd00h/64768d Inode: 2753332     Links: 1
Access: (0755/-rwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2025-02-06 02:37:39.019569599 +0000
Modify: 2023-10-31 11:35:56.000000000 +0000
Change: 2023-11-16 06:12:11.410744056 +0000
 Birth: -

> stat /bin/find
  File: /bin/find
  Size: 320160     Blocks: 632        IO Block: 4096   regular file
Device: fd00h/64768d Inode: 2753161     Links: 1
Access: (0755/-rwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2025-02-06 06:25:01.632548914 +0000
Modify: 2020-02-18 01:05:59.000000000 +0000
Change: 2022-03-16 11:37:59.443857825 +0000
 Birth: -

> stat /bin/top
  File: /bin/top
  Size: 129072     Blocks: 256        IO Block: 4096   regular file
Device: fd00h/64768d Inode: 2758735     Links: 1
Access: (0755/-rwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2025-02-05 12:09:28.561120753 +0000
Modify: 2023-10-31 11:35:56.000000000 +0000
Change: 2023-11-16 06:12:11.410744056 +0000
~~~

**发现像ls, lsattr, chattr这几个命令都被篡改了。**

目前使用apt重新安装是做不到了，因为在覆盖文件时是拿不到权限的。

下载解压对应版本的coreutils和e2fsprogs的deb包，解压。

~~~bash
> wget http://archive.ubuntu.com/ubuntu/pool/main/e/e2fsprogs/e2fsprogs_1.45.5-2ubuntu1.1_amd64.deb
> dpkg-deb -x e2fsprogs_1.45.5-2ubuntu1.1_amd64.deb  extracted2/
~~~

先对比下正常的chattr与被篡改的chattr
~~~bash
> stat chattr
  File: chattr
  Size: 14656      Blocks: 32         IO Block: 4096   regular file
Device: fd00h/64768d Inode: 6964046     Links: 1
Access: (0755/-rwxr-xr-x)  Uid: ( 1012/     winka)   Gid: ( 1012/     winka)
Access: 2025-02-06 13:43:19.963694821 +0000
Modify: 2022-06-02 00:59:32.000000000 +0000
Change: 2025-02-06 13:43:19.963694821 +0000
 Birth: -

> stat /usr/bin/chattr
  File: /usr/bin/chattr
  Size: 73         Blocks: 8          IO Block: 4096   regular file
Device: fd00h/64768d Inode: 2814158     Links: 1
Access: (0755/-rwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2025-02-05 16:47:04.362367974 +0000
Modify: 2024-02-06 19:33:23.323953572 +0000
Change: 2024-02-06 19:33:23.327953586 +0000
~~~

使用解压后包中的chattr(/usr/bin下)修改/bin路径下的chattr权限然后替换它

~~~bash
> sudo extracted2/usr/bin/chattr -ia /bin/chattr
~~~

同样逻辑处理lsattr

这两项核心功能处理完后，使用apt重新安装coreutils和e2fsprogs两个包

同样处理掉定时任务

~~~bash
> sudo chattr -ia /etc/cron.hourly/0
> sudo rm -rf /etc/cron.hourly/0
~~~

保险起见，其他的核心功能也重新安装

~~~bash
sudo apt reinstall coreutils e2fsprogs findutils procps
~~~

安装debsum来检查其他的包是否有问题

~~~bash
> sudo debsums -c
/usr/sbin/rsyslogd
/usr/sbin/rmt-tar
~~~

重新安装

~~~中间同样有因为权限篡改安装失败的例子
> sudo lsattr /usr/sbin/rsyslogd
-----a--------e----- /usr/sbin/rsyslogd
> sudo lsattr /var/log/syslog
----ia--------e----- /var/log/syslog
~~~

重新分配属性即可

~~~bash
> sudo chattr -ia /var/log/syslog
> sudo chattr -ia /usr/sbin/rsyslogd
~~~

至此结束

---

更新:

nvidia-smi命令被创建了alias映射到其他命令，gpustat和nvidia-smi输出的结果不一样。

~~~
type -a nvidia-smi
nvidia-smi is aliased to `cat ~/.config/nvidia_smi_backup/nvidia-smi.log'
nvidia-smi is /usr/bin/nvidia-smi
nvidia-smi is /bin/nvidia-smi
~~~

该alias的设置一般是使用root权限直接加到所有用户的~/.bashrc下的...

创建时间是上一次攻击的时间...

## 2025.5.12 更新

挖矿程序已经不再出现了，以为这事情已经告一段落。结果中午突然有其他组同学告知我，我管理的服务器IP一直在爆破他们的服务器...

先检查root

~~~
ps -U root
~~~

发现其正在与一台服务器建立ssh连接

~~~
2065950 ssh xxx@211.87.xxx.xxx       0.0  0.0
~~~

继续查看该进程的详细指令

~~~
ps -fp 2065950
ps -o user,pid,ppid,cmd -p 2065950
~~~

~~~
sudo lsof -p 2065950
COMMAND     PID USER   FD   TYPE    DEVICE SIZE/OFF    NODE NAME
ssh     2065950 root  cwd    DIR     253,0     4096 3441959 /usr/games/lan
ssh     2065950 root  rtd    DIR     253,0     4096       2 /
~~~

很奇怪吧, 计算用的服务器上哪来games/lan, 到对应的目录下, 找到了其暴力破解的同一局域网下的其他服务器的用户名和密码。

当然我可以就此将其删除，但是问题是：**它是哪来的？** 上次以为已经将后门全部清理了，但现在看来，依然还有残留，攻击者依然有方法登入服务器。因为恶意程序的创建时间都是一天前，而对应的创建时间，仅有一个非root用户在线，因此，要么是该用户的登录方式泄露，且攻击者留了提升权限的后门，要么是root用户中保留着其他后门。

~~~
 ps -p 126850 -o etime,etime,stime,lstart
    ELAPSED     ELAPSED STIME                  STARTED
 1-20:04:32  1-20:04:32 May10 Sat May 10 18:05:38 2025
~~~

检查定期任务

在/etc/cron.hourly下，又发现了0文件，和上一次的是一样的。

至此，将以上发现的恶意程序全部删除（其又用chattr修改了权限，但这次似乎没有将coreutils替换），然后添加监控。

~~~
sudo auditctl -w /etc/cron.hourly/ -p wa -k cron_hourly_all
~~~

检查命令

~~~
sudo ausearch -k cron_hourly_all
~~~

---

**等待上钩**

---

## 2025.8.18 更新 没抓到

检查日志结果如下：
~~~bash 
sudo journalctl --since "2025-08-12 06:50" --until "2025-08-12 07:10" 
~~~ 

**在06:54:07：systemd 启动 Daily apt upgrade and clean activities**
~~~bash
Aug 12 06:54:07 king systemd[1]: Starting Daily apt upgrade and clean activities...  06:54:37 king systemd-networkd-wait-online[2160053]: Event loop failed: Connection timed out 
Aug 12 06:54:37 king kernel: NVRM: GPU 0000:39:00.0: RmInitAdapter failed! (0x62:0x40:2393) 
Aug 12 06:54:37 king kernel: NVRM: GPU 0000:39:00.0: rm_init_adapter failed, device minor number 2 
Aug 12 06:54:39 king kernel: NVRM: GPU 0000:39:00.0: RmInitAdapter failed! (0x62:0x40:2393) 
Aug 12 06:54:39 king kernel: NVRM: GPU 0000:39:00.0: rm_init_adapter failed, device minor number 2 
Aug 12 06:54:39 king kernel: NVRM: GPU 0000:39:00.0: RmInitAdapter failed! (0x62:0x40:2393) 
Aug 12 06:54:39 king kernel: NVRM: GPU 0000:39:00.0: rm_init_adapter failed, device minor number 2 
Aug 12 06:54:41 king kernel: NVRM: GPU 0000:39:00.0: RmInitAdapter failed! (0x62:0x40:2393) 
Aug 12 06:54:41 king kernel: NVRM: GPU 0000:39:00.0: rm_init_adapter failed, device minor number 2 
Aug 12 06:54:41 king kernel: NVRM: GPU 0000:39:00.0: RmInitAdapter failed! (0x62:0x40:2393) 
Aug 12 06:54:41 king kernel: NVRM: GPU 0000:39:00.0: rm_init_adapter failed, device minor number 2 
Aug 12 06:54:41 king audit[2153877]: ANOM_ABEND auid=1016 uid=1016 gid=1016 ses=4543 pid=2153877 comm="node" exe="/home/zpp/.vscode-server/cli/servers/Stable-cb0c47c0cfaad0757385834bd89d410c78a856c0/server/node" sig=6 res=1 
Aug 12 06:55:01 king audit[2161070]: USER_ACCT pid=2161070 uid=0 auid=4294967295 ses=4294967295 msg='op=PAM:accounting grantors=pam_permit acct="root" exe="/usr/sbin/cron" hostname=? addr=? terminal=cron res=success' 
Aug 12 06:55:01 king audit[2161070]: CRED_ACQ pid=2161070 uid=0 auid=4294967295 ses=4294967295 msg='op=PAM:setcred grantors=pam_permit,pam_cap acct="root" exe="/usr/sbin/cron" hostname=? addr=? terminal=cron res=success' 
Aug 12 06:55:01 king audit[2161070]: SYSCALL arch=c000003e syscall=1 success=yes exit=1 a0=7 a1=7ffe0108d210 a2=1 a3=7f32217d6371 items=0 ppid=2381 pid=2161070 auid=0 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=(none) ses=587> 
Aug 12 06:55:01 king audit: PROCTITLE proctitle=2F7573722F7362696E2F43524F4E002D66 
Aug 12 06:55:01 king CRON[2161070]: pam_unix(cron:session): session opened for user root by (uid=0) 
Aug 12 06:55:01 king audit[2161070]: USER_START pid=2161070 uid=0 auid=0 ses=5871 msg='op=PAM:session_open grantors=pam_loginuid,pam_env,pam_env,pam_permit,pam_umask,pam_unix,pam_limits acct="root" exe="/usr/sbin/cron" hostname=? addr=? term> 
Aug 12 06:55:01 king CRON[2161071]: (root) CMD (command -v debian-sa1 > /dev/null && debian-sa1 1 1) 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' resumed (module 'builtin:omfile') [v8.2001.0 try https://www.rsyslog.com/e/2359 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' suspended (module 'builtin:omfile'), retry 0. There should be messages before this one giving the reason for suspension. [v8.2001.0 try https://www.rsyslog.com/e/2007 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' resumed (module 'builtin:omfile') [v8.2001.0 try https://www.rsyslog.com/e/2359 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' suspended (module 'builtin:omfile'), retry 0. There should be messages before this one giving the reason for suspension. [v8.2001.0 try https://www.rsyslog.com/e/2007 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' resumed (module 'builtin:omfile') [v8.2001.0 try https://www.rsyslog.com/e/2359 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' suspended (module 'builtin:omfile'), retry 0. There should be messages before this one giving the reason for suspension. [v8.2001.0 try https://www.rsyslog.com/e/2007 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' resumed (module 'builtin:omfile') [v8.2001.0 try https://www.rsyslog.com/e/2359 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' suspended (module 'builtin:omfile'), retry 0. There should be messages before this one giving the reason for suspension. [v8.2001.0 try https://www.rsyslog.com/e/2007 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' resumed (module 'builtin:omfile') [v8.2001.0 try https://www.rsyslog.com/e/2359 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' suspended (module 'builtin:omfile'), retry 0. There should be messages before this one giving the reason for suspension. [v8.2001.0 try https://www.rsyslog.com/e/2007 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' resumed (module 'builtin:omfile') [v8.2001.0 try https://www.rsyslog.com/e/2359 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' suspended (module 'builtin:omfile'), retry 0. There should be messages before this one giving the reason for suspension. [v8.2001.0 try https://www.rsyslog.com/e/2007 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' resumed (module 'builtin:omfile') [v8.2001.0 try https://www.rsyslog.com/e/2359 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' suspended (module 'builtin:omfile'), retry 0. There should be messages before this one giving the reason for suspension. [v8.2001.0 try https://www.rsyslog.com/e/2007 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' resumed (module 'builtin:omfile') [v8.2001.0 try https://www.rsyslog.com/e/2359 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' suspended (module 'builtin:omfile'), retry 0. There should be messages before this one giving the reason for suspension. [v8.2001.0 try https://www.rsyslog.com/e/2007 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' resumed (module 'builtin:omfile') [v8.2001.0 try https://www.rsyslog.com/e/2359 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' suspended (module 'builtin:omfile'), retry 0. There should be messages before this one giving the reason for suspension. [v8.2001.0 try https://www.rsyslog.com/e/2007 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' resumed (module 'builtin:omfile') [v8.2001.0 try https://www.rsyslog.com/e/2359 ] 
Aug 12 06:55:01 king audit[2161070]: CRED_DISP pid=2161070 uid=0 auid=0 ses=5871 msg='op=PAM:setcred grantors=pam_permit acct="root" exe="/usr/sbin/cron" hostname=? addr=? terminal=cron res=success' 
Aug 12 06:55:01 king audit[2161070]: USER_END pid=2161070 uid=0 auid=0 ses=5871 msg='op=PAM:session_close grantors=pam_loginuid,pam_env,pam_env,pam_permit,pam_umask,pam_unix,pam_limits acct="root" exe="/usr/sbin/cron" hostname=? addr=? termi> 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' suspended (module 'builtin:omfile'), retry 0. There should be messages before this one giving the reason for suspension. [v8.2001.0 try https://www.rsyslog.com/e/2007 ] 
Aug 12 06:55:01 king rsyslogd[2157]: action 'action-2-builtin:omfile' suspended (module 'builtin:omfile'), next retry is Tue 
Aug 12 06:55:31 2025, retry nbr 0. There should be messages before this one giving the reason for suspension. [v8.20> 
Aug 12 06:55:01 king CRON[2161070]: pam_unix(cron:session): session closed for user root 
~~~

**06:55:17：在该服务运行窗口内，审计日志记录到/etc/cron.hourly/0相关的内容**
~~~
Aug 12 06:55:17 king audit[2161431]: SYSCALL arch=c000003e syscall=257 success=yes exit=4 a0=ffffff9c a1=562c591b32c0 a2=242 a3=1b6 items=2 ppid=2161184 pid=2161431 auid=4294967295 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=> 
Aug 12 06:55:17 king audit: CWD cwd="/var/backups" 
Aug 12 06:55:17 king audit: PATH item=0 name="/etc/cron.hourly/" inode=1310745 dev=fd:00 mode=040755 ouid=0 ogid=0 rdev=00:00 nametype=PARENT cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0 
Aug 12 06:55:17 king audit: PATH item=1 name="/etc/cron.hourly/0" inode=1315595 dev=fd:00 mode=0100644 ouid=0 ogid=0 rdev=00:00 nametype=CREATE cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0 
Aug 12 06:55:17 king audit: PROCTITLE proctitle=6C73002D7274002F7661722F63616368652F6170742F61726368697665732F2F2A2E646562 
Aug 12 06:55:17 king audit[2161431]: SYSCALL arch=c000003e syscall=257 success=yes exit=5 a0=ffffff9c a1=562c591b34c0 a2=242 a3=1b6 items=2 ppid=2161184 pid=2161431 auid=4294967295 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=> 
Aug 12 06:55:17 king audit: CWD cwd="/var/backups" 
Aug 12 06:55:17 king audit: PATH item=0 name="/etc/cron.hourly/" inode=1310745 dev=fd:00 mode=040755 ouid=0 ogid=0 rdev=00:00 nametype=PARENT cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0 
Aug 12 06:55:17 king audit: PATH item=1 name="/etc/cron.hourly/0" inode=1315595 dev=fd:00 mode=0100644 ouid=0 ogid=0 rdev=00:00 nametype=NORMAL cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0 
Aug 12 06:55:17 king audit: PROCTITLE proctitle=6C73002D7274002F7661722F63616368652F6170742F61726368697665732F2F2A2E646562 
Aug 12 06:55:17 king audit[2161431]: SYSCALL arch=c000003e syscall=91 success=yes exit=0 a0=5 a1=bff a2=0 a3=1b6 items=1 ppid=2161184 pid=2161431 auid=4294967295 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=(none) ses=42949672> 
Aug 12 06:55:17 king audit: PATH item=0 name=(null) inode=1315595 dev=fd:00 mode=0100644 ouid=0 ogid=0 rdev=00:00 nametype=NORMAL cap_fp=0 cap_fi=0 cap_fe=0 cap_fver=0 cap_frootid=0 
Aug 12 06:55:17 king audit: PROCTITLE proctitle=6C73002D7274002F7661722F63616368652F6170742F61726368697665732F2F2A2E646562 
~~~

**创建动作与apt每日升级流程同步完成**

~~~
Aug 12 06:55:17 king systemd[1]: apt-daily-upgrade.service: Succeeded. 
Aug 12 06:55:17 king systemd[1]: Finished Daily apt upgrade and clean activities. 
Aug 12 06:55:17 king audit[1]: SERVICE_START pid=1 uid=0 auid=4294967295 ses=4294967295 msg='unit=apt-daily-upgrade comm="systemd" exe="/usr/lib/systemd/systemd" hostname=? addr=? terminal=? res=success' 
Aug 12 06:55:17 king audit[1]: SERVICE_STOP pid=1 uid=0 auid=4294967295 ses=4294967295 msg='unit=apt-daily-upgrade comm="systemd" exe="/usr/lib/systemd/systemd" hostname=? addr=? terminal=? res=success'
~~~

检查

~~~bash
> systemctl cat apt-daily-upgrade.service
# /lib/systemd/system/apt-daily-upgrade.service
[Unit]
Description=Daily apt upgrade and clean activities
Documentation=man:apt(8)
ConditionACPower=true
After=apt-daily.service network.target network-online.target systemd-networkd.service NetworkManager.service connman.service

[Service]
Type=oneshot
ExecStartPre=-/usr/lib/apt/apt-helper wait-online
ExecStart=/usr/lib/apt/apt.systemd.daily install
KillMode=process
TimeoutStopSec=900
~~~

其中apt-daily-upgrade.service是正常的每日apt升级和清理


测试了手动启动apt-daily-upgrade.service并不会导致/etc/cron.hourly/0的创建

没抓到，继续等待

~~~base
# 已创建的监控规则
sudo auditctl -l
-w /etc/cron.hourly/0 -p rwxa -k cron0_watch
-w /etc/system -p rwxa -k system_watch
~~~


~~~
sudo ausearch -k cron0_watch --format text

sudo ausearch -k system_watch --format text
~~~