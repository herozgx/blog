---
title: yum详解
date: 2016-08-29 23:14:39
tags:
---

## yum简介
通常一个软件在不同平台下的二进制格式是不同的， 例如在i386平台下编译的二进制程序拿到X86-64平台上是不能用的。所以通常要在Linux下安装一个软件， 需要将软件源码下载下来在本地进行编译和安装（./configure, make, make install）。 为了解决这个繁琐的过程， 各Linux发行厂商想出了个办法， 世界上的平台无非就那么多种， 我在每种平台上对软件进行编译， 将编译好的二进制程序提供给用户， 用户根据自己的平台各取所需就行了， 不需要再进行编译了。 这就是各Linux发行版的软件包管理方式：在Redhat/CentOS/Fedora采用[RPM包管理](/blog/2016/08/30/rpm-detail)。

<!-- more -->
这个方式确实解决了多次编译的问题，但是没有解决软件包的依赖关系问题。 通常软件都是建立在其他软件提供的功能基础上的，例如nginx依赖于perl的正则表达式模块儿、 依赖于zlib压缩等。 我们在安装一个软件前， 要优先安装好它所依赖的所有软件包。而它依赖的软件包很有可能还会依赖其他软件包，以此类推， 安装一个软件的过程太过复杂， 太耗费精力。

该如何解决呢？ 

东方红，太阳升，YUM诞生了！

YUM(Yellowdog Updater, Modified)是采用RPM包管理的Linux发行版的命令行和包管理程序。YUM解决了RPM包间的依赖关系问题


yum分为客户端和服务端。 客户端即yum命令， 通过它可以自动安装rpm包及其依赖包； 服务端即yum源站（仓库），提供一个基本的文件服务， 可以是HTTP、FTP、文件系统。


## yum命令

当我们安装上任何一个Linux发行版后， 就已经配置好了一个或多个YUM仓库供我们使用， 所以只要机器可以联网， 那么一般yum就可以使用了。 

### yum命令格式:
```
yum [选项] [command] [参数]
```
### 常用命令
* install
```
yum install -y gcc       # 安装软件包， 这是最常用的命令
```

* remove | erase
```
yum remove -y gcc        # 删除软件包， 同时删除依赖该软件包(gcc)的软件包。
```

* list & info
这两个命令的参数一样， list根据参数列出软件的状态（Installed、 Available）， info列出软件的description和summary信息信息，比如:
```
$ yum list gcc
Available Packages
gcc.x86_64                   4.4.7-17.el6                                base

$ yum info gcc
Available Packages
Name        : gcc
Arch        : x86_64
Version     : 4.4.7
Release     : 17.el6
Size        : 10 M
Repo        : base
Summary     : Various compilers (C, C++, Objective-C, Java, ...)
URL         : http://gcc.gnu.org
License     : GPLv3+ and GPLv3+ with exceptions and GPLv2+ with exceptions
Description : The gcc package contains the GNU Compiler Collection version 4.4.
            : You will need this package in order to compile C code.

```
* search
当你知道一些关于软件包的信息， 但是又不确定它的名字时, 可以用search命令。 它会优先查找软件名和Summary信息， 若找不到才会去搜索描述信息(Description).
```
yum search 关键字                 # 通过关键字查询yum仓库中匹配的软件名或软件描述信息
yum search all                    # 会列出仓库的所有软件包
```

* provides | whatprovides
用来查找一个文件属于哪个软件包， 文件名一般用绝对路径名， 可以用通配符
```
yum provides `which dig`
yum provides /etc/named.conf
```

* clean
用来清理随着时间在yum缓存中逐渐积累起来的缓存， 路径一般为/var/cache/yum , clean后边可以加多种参数， all最常用：
```
yum clean all
yum clean metadata
yum clean packages
```

* makecache
将可用的仓库中的元数据缓存到本地(默认为/var/cache/yum/目录中)， 增加查询速度

* repolist
列出仓库信息
```
yum repolist all
```

## 创建自己的yum仓库

yum在线机制是一种C/S架构的服务， Client即上一节介绍的yum命令， Server则是一个提供文件服务的服务器，可以是：
* FTP
* WEB
* FILE

创建一个yum仓库有以下几个步骤：
1. 安装创建仓库的软件 `createrepo`
```
yum install createrepo -y
```
2. 创建一个目录作为仓库目录
```
mkdir /data         # 目录位置根据自己情况自己指定
```
3. 将RPM包放入仓库目录
```
cd /data
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/bind-9.8.2-0.47.rc1.el6.x86_64.rpm
```
4. 执行createrepo命令
createrepo命令需要指定一个目录作为参数， 它在该目录下创建一个repodata的目录来保存仓库的元数据。 每当我们向仓库中添加RPM包时，都需要重新执行createrepo生成元数据信息。  
假如一个仓库中有很多软件包， 已经用createrepo生成了元数据目录repodata， 此时我们又新增加了1个RPM包， 若重新生成repodata目录代价太大， 我们可以用 `--update` 只对新rpm包建立元数据信息 , 其他RPM包复用原有的元数据信息。
```
createrepo /data 
createrepo --update /data
```
5. 配置仓库文件
在第4步中，仓库已经建立好了， 我们已经可以利用该仓库进行软件的更新了。 这里有3中方式访问到该仓库（/data目录）: HTTP、FTP、本地访问
   * WEB方式(http://)
	在我们的仓库服务器上搭建一个http服务器（Nginx、Apache、Lighttpd等）, 指定一个uri映射到/data即可。
	```
	[base]
	name=My Own Repo
	baseurl=http://myrepo.domian.com/data/
	enabled=1
	gpgcheck=0
	```
   * FTP方式(ftp://)
	在仓库服务器上搭建一个匿名FTP服务器（proftpd，vsftpd等），将匿名账号家目录指定为/data
	```
	[base]
	name=My Own Repo
	baseurl=ftp://192.168.1.100/
	enabled=1
	gpgcheck=0
	```
   * 本地文件或NFS挂载(file:///data)
	只能供仓库服务器自身的yum使用; 或者配置网络文件系统（NFS）实现其他挂载主机的访问。
	```
	[base]
	name=My Own Repo
	baseurl=file:///data/
	enabled=1
	gpgcheck=0
	```


## 国内较好的CentOS源站推荐

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
