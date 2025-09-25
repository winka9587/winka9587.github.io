---
title: DROID数据集下载
date: 2025-09-18 17:51
category: 
author: 
tags: []
summary: 
---

## 安装Google Cloud CLI(包含gsutil)

### 无法定位软件包 google-cloud-cli

确保有必要的依赖
~~~
sudo apt-get install -y apt-transport-https ca-certificates gnupg curl
~~~

删除旧的cloud.google的GPG key
~~~
sudo rm -f /usr/share/keyrings/cloud.google.gpg
~~~

重新添加 Google Cloud 公钥
~~~
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | \
sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg
~~~

配置 apt 源
~~~
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | \
sudo tee /etc/apt/sources.list.d/google-cloud-sdk.list
~~~

更新并安装
~~~
sudo apt-get update
sudo apt-get install -y google-cloud-cli
~~~

## 下载

下载sample数据集用于测试

~~~
gsutil -m cp -r gs://gresearch/robotics/droid_100 .
~~~

下载完整数据集

~~~
gsutil -m cp -r gs://gresearch/robotics/droid .
~~~

如果中间下载失败

> CommandException: 81 files/objects could not be transferred./s ETA 00:40:09

可以使用

~~~
gsutil -m rsync -r -c gs://gresearch/robotics/droid ./droid/
~~~

来补全未下载成功的文件