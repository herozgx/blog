---
title: yum详解
date: 2016-08-29 23:14:39
tags:
---

## yum简介
通常一个软件在不同平台下的二进制格式是不同的， 例如在i386平台下编译的二进制程序拿到X86-64平台上是不能用的。所以通常要在Linux下安装一个软件， 需要将软件源码下载下来在本地进行编译和安装（./configure, make, make install）。 为了解决这个繁琐的过程， 各Linux发行厂商想出了个办法， 世界上的平台无非就那么多种， 我在每种平台上对软件进行编译， 将编译好的二进制程序提供给用户， 用户根据自己的平台各取所需就行了， 不需要再进行编译了。 这就是各Linux发行版的软件包管理方式：在Redhat/CentOS/Fedora采用[RPM包管理](/blog/2016/08/30/rpm-detail)。

这个方式确实解决了多次编译的问题，但是没有解决软件包的依赖关系问题。 通常软件都是建立在其他软件提供的功能基础上的，例如nginx依赖于perl的正则表达式模块儿、 依赖于zlib压缩等。 我们在安装一个软件前， 要优先安装好它所依赖的所有软件包。而它依赖的软件包很有可能还会依赖其他软件包，以此类推， 安装一个软件的过程太过复杂， 太耗费精力。

该如何解决呢？ 

东方红，太阳升，YUM诞生了！

YUM(Yellowdog Updater, Modified)是采用RPM包管理的Linux发行版的命令行和包管理程序。YUM解决了RPM包间的依赖关系问题


yum分为客户端和服务端。 客户端即yum命令， 通过它可以自动安装rpm包及其依赖包； 服务端即yum源站（仓库），提供一个基本的文件服务， 可以是HTTP、FTP、文件系统。




## yum源站搭建


## yum客户端配置

### 1. 基本配置样例及说明：
```
[base]
name=CentOS-$releasever - Base - 163.com
baseurl=http://mirrors.163.com/centos/$releasever/os/$basearch/
enabled=1
gpgcheck=1
gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-6
```

* **[base]**源站名
* **name**源站名
* **baseurl**: 源站路径， 可以是http、ftp、file协议等
* **enabled**: 是否启用， 0禁用， 1启用

### 2. 变量说明


### 3. 国内较好的CentOS源站推荐

阿里镜像
```
# centos-5
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo
# centos-6
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
# centos-7
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo
```

网易镜像
```
# centos-5
wget -O /etc/yum.repos.d/CentOS5-Base-163.repo http://mirrors.163.com/.help/CentOS5-Base-163.repo 
# centos-6
wget -O /etc/yum.repos.d/CentOS6-Base-163.repo http://mirrors.163.com/.help/CentOS6-Base-163.repo 
# centos-7
wget -O /etc/yum.repos.d/CentOS7-Base-163.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo 
```
