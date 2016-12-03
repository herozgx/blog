---
title: bonding技术及配置
date: 2016-08-30 00:10:42
tags:
---

## 什么是bonding？
通俗的讲， bonding技术就是讲多块网络设备组成一个网卡的技术。 从而提升了该虚拟接口的吞吐量和高可用性。

## bonding原理
在正常情况下，网卡只接收目的硬件地址(MAC Address)是自身MAC的以太网帧，过滤别的数据帧，以减轻驱动程序的负担；但是网卡也支持另外一种被称为混杂promisc的模式，可以接收网络上所有的帧，bonding就运行在这种模式下，而且修改了驱动程序中的mac地址，将两块网卡的MAC地址改成相同，可以接收特定MAC的数据帧。然后把相应的数据帧传送给bond驱动程序处理
<!-- more -->

## bonding配置

### 1.建立bond网卡配置文件ifcfg-bond0
```
DEVICE=bond0
BOOTPROTO=dhcp
ONBOOT=yes
USERCTL=NO
BONDING_OPTS="mode=0 miimon=100"
```

> BONDING_OPTS


### 2.配置两块真实网卡配置文件
ifcfg-eth0:
```
DEVICE=eth0
BOOTPROTO=none
ONBOOT=yes
MASTER=bond0
SLAVE=yes
```

ifcfg-eth1:
```
DEVICE=eth1
BOOTPROTO=none
ONBOOT=yes
MASTER=bond0
SLAVE=yes
```


### 3. 加载bonding模块
通过modprobe加载bonding模块
```
modprobe bonding
```
也可以配置成开机自动加载bonding模块， 在 /etc/modprobe.d/bonding.conf 中添加如下配置：
```
alias bond0 bonding
```

### 4. 启动bond0
```
ifup bond0
```

### 5. 查看bond0相关信息， 验证带宽
```
[root@localhost ~]# cat /proc/net/bonding/bond0 
Ethernet Channel Bonding Driver: v3.7.1 (April 27, 2011)

Bonding Mode: load balancing (round-robin)
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 0
Down Delay (ms): 0

Slave Interface: eno33554984
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 00:0c:29:43:46:de
Slave queue ID: 0

Slave Interface: eno50332208
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 00:0c:29:43:46:e8
Slave queue ID: 0
```

查看带宽信息
```
ethtool bond0
```
