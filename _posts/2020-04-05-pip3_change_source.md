---
layout: post
title: "pip3换国内源"
tags: [Python, pip]
comments: true
---

```shell
cd ~/.pip
如果不存在.pip则创建目录
mkdir ~/.pip
cd ~/.pip
 
 
touch pip.conf
sudo gedit ~/.pip/pip.conf
 
 
在pip.conf中写入如下内容：即可 
 
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple/ 
[install]
trusted-host = pypi.tuna.tsinghua.edu.cn
```