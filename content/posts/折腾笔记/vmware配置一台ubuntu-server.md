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

发送宿主机公钥给vmware虚拟机，允许宿主机免密登入虚拟机

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub root@u
```

​    

## 关于vmware

### 共享文件

vmware可以将宿主机的某些文件夹共享给虚拟机的,步骤如下：

![](https://raw.githubusercontent.com/biningo/cdn/master/img/vmware1.png)

### 网络模式

- **桥接模式**

vmware设置为 **桥接模式**，这样虚拟机的IP网络就相当于一台独立的电脑，与虚拟机没有任何关联，只要在这个局域网内都可以链接，并且我们必须手动给他们分配一个有效的 **IP地址**

桥接模式的架构图如下：

![](https://raw.githubusercontent.com/biningo/cdn/master/img/vmware-2.png)

- **NAT模式**

1. NAT类型称为网络地址转换模式
2. 虚拟机之间、虚拟机和宿主机之间、虚拟机和外部host之间都可以进行通信，但是外部无法访问到内网
3. 虚拟机的IP只需要配置NAT网段中的IP，访问外部host可以通过宿主机IP访问。它不需要有外部网络独立的IP（即物理交换机网段中的IP）优点：虚拟机的网络配置确定后，就可以很少变动。因为NAT配置不变，宿主机连接的网络变化，不影响虚拟机。所以大部分集群实验选择的就是NAT模式

![](https://raw.githubusercontent.com/biningo/cdn/master/img/nat.png)

1. 按照上图，首先我们需要给`NAT`适配器分配一个子网号和子网掩码，比如`192.168.25.0/24`（可以随意分配任何一个私有网段），子网掩码为`255.255.255.0` ，该子网的第一个IP则会分配给这个适配器当做地址，比如`192.168.25.1` 
2. 然后我们需要设置该NAT网络的网关，该网关必须在子网内部，并且不可以使用适配器的地址，所以我们设置NAT子网内部的网关的地址为 `192.168.25.2` （也就是上面交换机的地址）
3. 网关有了，局域网就搭建好了，该NAT局域网内部的IP就必须要设置到该子网内部，发送信息的时候就会发送给交换机然后再由NAT地址转化转化为是自己地址然后访问外网

- **主机模式**

完全独立封闭的模式，虚拟机唯一能访问的就是宿主机

### 拍摄快照

拍摄快照用于保存当前状态，并且用于以后恢复

​    

## 参考

