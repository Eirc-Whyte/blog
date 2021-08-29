---
title: Ubuntu 18.04 手动编译Clang 环境配置
date: 2021-08-12 15:31:36
tags:
- Clang
categories:
- 环境配置
---

配环境跟我八字不合

<!--more-->

### 系统安装

首先，下载崭新的Ubuntu18.04.1.iso；

安装，过程中记得跳过语言包安装；

先换源：

```bash
cp /etc/apt/sources.list /etc/apt/srcbk.list
vi /etc/apt/sources.list
```

备份并打开源文件配置文件，写入[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)：

```bash
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
```

更新apt：

```shell
apt update && apt upgrade
```

安装gnome-tweaks-tools防止4k屏下字太小瞎眼

```shell
apt install gnome-tweaks-tools
```

打开tweaks->Fonts->Scaling Factor = 1.75

字大了图标小，因此缩放一下图标：![image-20210812153907212](https://ericblog.oss-cn-beijing.aliyuncs.com/img/image-20210812153907212.png)

护眼工程结束

### 安装编译工具

安装编译套件：

```shell
apt install g++
apt install gcc
apt install make
```

差个cmake，因为18.04apt中默认cmake版本是3.10，我们需要更高版本的cmake，因此手动安装一下，到[官网](https://cmake.org/download/)下载最新版本的cmake*.sh

![image-20210812154616641](https://ericblog.oss-cn-beijing.aliyuncs.com/img/image-20210812154616641.png)

扔到虚拟机文件夹，运行：

```shell
bash ./cmake-3.21.1-linux-x86_64.sh
```

回车点到底，再加两个y，你会发现就装好了

```shell
cp ./cmake-3.21.1-linux-x86_64/ /usr/local/cmake -r
ln -sf /usr/local/cmake/bin/* /usr/bin/
```

拷贝到/usr/local/并创建软链接，创建软链接是为了让普通用户也可以使用，否则只有sudo可以使用

由于没安装第三方shell，使用默认bash的情况下，修改环境变量，并激活：

```shell
vi ~/.bashrc
// in ~./bashrc
...
export PATH=/usr/local/cmake/bin:$PATH
source ~/.bashrc
```

装完测试一下：

```shell
sudo cmake --version
cmake --version
```

看两者是不是都可以用，版本是不是相同

### 安装、编译llvm

llvm实在太 大 了

目前尝试过能成功下载的方法只有(在windows下)

```shell
git clone https://mirrors.tuna.tsinghua.edu.cn/help/llvm-project.git/ --depth=1
```

获取最新分支，然后再获取完整仓库（不获取其实也能用）

```shell
git fetch --unshallow
```

完整版大概不到2GB

打包扔到虚拟机

```shell
cd llvm-project
mkdir build
cd build
cmake -G "Unix Makefiles" ../llvm
```

将目标文件编译到build文件夹，大概需要一到两小时左右（硬件环境：Intel 8250U）

