---
title: vmware配置一台ubuntu-server
date: 2021-02-15
categories: [折腾笔记]
tags: [linux,安装,折腾]
---

## 配置步骤

ubuntu阿里镜像站：http://mirrors.aliyun.com/ubuntu-releases/

ubuntu配置阿里云软件源：https://developer.aliyun.com/mirror/ubuntu?spm=a2c6h.13651102.0.0.3e221b11Zy3CPT

更新软件源头

```bash
$ apt update
```

重置root密码：

```bash
$ su passwd
```

设置允许以`root`身份ssh登入，设置`PermitRootLogin yes`：

```bash
$ vim /etc/ssh/sshd_config #设置PermitRootLogin yes
$ service ssh  restart #重启ssh服务
```

静态IP和网络相关的设置

```bash
$ vim /etc/netplan/00-installer-config.yaml
----------------------------------------------
# This is the network config written by 'subiquity'
network:
  ethernets:
    ens33: #网卡名字
      addresses:
      - 192.168.1.10/24 #IP
      dhcp4: false #关闭DHCP
      gateway4: 192.168.1.1 #设置网关 4表示ipv4
      nameservers: #设置DNS
          addresses:
          - 192.168.1.1
  version: 2
-------------------------------------------------
$ netplan apply #应用配置
```

​    

​    

## 关于vmware

### 共享文件

vmware可以将宿主机的某些文件夹共享给虚拟机的,步骤如下：

![](https://raw.githubusercontent.com/biningo/cdn/master/img/vmware1.png)

### 网络桥接模式

vmware设置为 **桥接模式**，这样虚拟机的IP网络就相当于一台独立的电脑，与虚拟机没有任何关联，只要在这个局域网内都可以链接，并且我们必须手动给他们分配一个有效的 **IP地址**

桥接模式的架构图如下：

![](https://raw.githubusercontent.com/biningo/cdn/master/img/vmware-2.png)

### 拍摄快照

拍摄快照用于保存当前状态，并且用于以后恢复

​    

## 参考

