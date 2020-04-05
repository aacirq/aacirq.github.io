---
layout: post
title: "Ubuntu18.04 安装 MATLAB R2018"
tags: [Ubuntu, MATLAB]
comments: true
---

MATLAB R2018有两个`iso`文件，叫`R2018a_glnxa64_dvd1.iso`和`R2018a_glnxa64_dvd2.iso`。

## 安装

假设这两个文件在`MATLAB_install`下。

首先，终端进入`MATLAB_install`文件夹下，然后执行命令新建文件夹
```shell
$ mkdir matlab
```

然后，挂载`R2018a_glnxa64_dvd1.iso`到`matlab`文件夹下，命令

```shell
$ sudo mount -t auto -o loop R2018a_glnxa64_dvd1.iso matlab/
```

执行命令运行安装程序

```shell
$ sudo ./matlab/install
```

然后按照流程安装软件

当安装到一半多的时候，会提示**弹出`dvd1`，挂载`dvd2`**，然后输入以下命令，挂载`dvd2`

```shell
$ sudo mount -t auto -o loop R2018a_glnxa64_dvd2.iso matlab/
```

然后按照界面提示继续安装。

然后解除挂载，执行以下命令两次

```shell
$ sudo umount -l ./matlab/
```


## 激活

首先运行`matlab`
```
$ sudo /usr/local/MATLAB/R2018a/matlab
```

即可进入激活界面，选择激活文件-`license_standalone.lic`

然后复制文件

```shell
$ sudo cp -r <crack_folder>/R2018a /usr/local/MATLAB/
```

这样就可以通过命令行运行`matlab`了。


## 运行

### 方法一：快捷命令行

首先，可以建立链接，以后直接在命令行输入`matlab`即可运行，命令为
```shell
$ sudo ln -s /usr/local/MATLAB/R2017a/bin/matlab  /usr/local/bin/matlab
```

### 方法二：建立快捷图标
然后，可以建立快捷图标

```shell
$ touch /usr/share/applications/MATLAB.desktop
```
用编辑器打开`MATLAB.desktop`文件（注意要用`sudo`）
输入以下内容
```
[Desktop Entry]

Name = matlab
Exec=/usr/local/MATLAB/R2018a/bin/matlab -desktop %F
Icon=/usr/local/MATLAB/R2018a/toolbox/shared/dastudio/resources/MatlabIcon.png
Encoding=UTF-8
Comment=matlab
StartupNotify=true
Terminal=false
Type=Application
```

需要注意的是，`Exec`后面是可执行文件的路径，`Icon`后面是图标的路径

然后，需要更改`/home/renxin/.matlab`文件夹的权限，不然打开`MATLAB`后会提示`桌面配置保存失败`，命令为
```shell
sudo chmod 777 /home/renxin/.matlab -R
```

这样之后，就可以直接利用图标打开`MATLAB`了。