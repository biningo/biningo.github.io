---
title: Linux常用命令总结
date: 2021-02-23
categories: [Linux]
tags: [Linux]
---

> **持续跟新.....**

## 进程相关

进程的信息都在`/proc`下

### ps

`ps` 显示系统进程

```bash
ps -ef
ps -aux
ps -ef | grep sshd
```

### jobs fg bg & nohub 

`&`  `jobs` `fg` `bg` `nohub`

```bash
./exe & #后台运行
./exe > log.txt 2>&1 & #0:标准输入 1:标准输出 2:标准错误 2>&1是将标准出错重定向到标准输出 最终结果就是`标准输出`和`错误`都被重定向到`log.txt`中
etcd > /dev/null  2>&1 & #后台运行 同时输出日志重定向到垃圾桶
nohup etcd > /dev/null 2>&1 & #以守护进程方式运行 不会随着终端退出而退出
jobs #查看后台运行中的进程

#ctrl+z暂停任务 ctrl+c终止任务
fg 1 #将后台任务放到前台执行
bg 1 #将一个暂停的任务放在后台执行
```

### kill

`kill`  `killall` 

```bash
kill -9 1111 #强制终止
killall 1111 #杀死进程 以及所有子进程
```

### pstree

`pstree`  显示进程树

```bash
pstree -p 111 #显示进程PID
pstree -u 111 #显示进程UID
```

### systemctl

`systemctl`(推荐)、service 系统服务

```bash
#系统服务都在此路径下:/usr/lib/systemd
systemctl start docker
systemctl stop docker 
systemctl restart docker
systemctl status docker
#开启/关闭服务开机启动
systemctl enable docker
systemctl disable docker
systemctl is-enabled docker

systemctl list-unit-files #列出所有守护进程的信息
```

### top

`top`

```bash
-c #显示完整的进程命令
-d 3 #更新周期3s
-n 2 #更新次数2
-p 111 #指定pid只显示进程
按键1 查看单独CPU的信息
按键s 修改更新时间
```

​    

## 网络相关

### utw

`utw` (centos下为`firewall`，ubuntu下为`utw`)    ubuntu下防火墙iptables工具

默认的配置文件`/etc/default/ufw `

```bash
#注意 没有出现在iptables中的一律不允许访问
ufw enable/disable
ufw status

#日志文件储存在 /var/logs/ufw
ufw logging on/off #启用关闭日志
ufw logging low|medium|high #设置日志级别

ufw allow 1000 #开放1000短端口
ufw deny 1000 #关闭1000端口 有数据包直接丢弃 无响应
ufw reject 1000 #关闭1000端口 有数据包接受则返回一个拒接包
ufw delete  reject 1000 #删除规则

ufw allow ssh #允许指定端口号或则对应的服务名 这里和22一样
ufw allow 80/tcp #还可以指定TCP或则UDP 只允许80端口上的TCP包
ufw allow http/tcp

ufw allow from 192.168.1.1 #允许指定IP连接到任何端口
ufw allow from 192.168.1.0/24 #允许特定子网连接到任何端口
ufw allow from 192.168.1.1 to any port 8080 #允许指定IP连接到8080端口 tcp/udp都可
ufw allow from 192.168.1.1 to any port 8080 proto udp #指定协议UDP

ufw status numbered #列出每个规则的序号 可以按照序号进行删除
ufw delete 1
```

### netstat/ss

`netstat` 监控网络状况

```bash
-a #显示所有socket
-i #显示网卡
-n #直接显示IP 不显示域名
-u #只查看UDP
-t #只查看TCP
-s #查看统计信息
-4 #只显示ipv4的套接字
-6 #只显示ipv6的套接字
-l #只查看监听中的socket
-p #显示那个PID在监听

netstat -a
netstat --anu #查看UDP连接情况
```

`ss`   是`Socket Statistics`的缩写，用来查看本机网络连接状况，和netstat一样，但是查询速度更快，用法和netstat一样

```bash
ss -ntl #查看TCP连接情况
ss -s #查看网络统计信息
```

### dig

`dig` 域名查询工具

```bash
dig @8.8.8.8 baidu.com #指定向8.8.8.8DNS服务器查询
dig @8.8.8.8 -p 53 baidu.com #还可以指定查询服务器的端口 默认是53
dig +trace baidu.com #显示整个DNS查询过程
dig +trace +additional baidu.com #显示更详细的信息
dig +short baidu.com #简化查询结果 只显示查询得到的IP
dig +x 111.111.111.111 #反向解析
dig ns baidu.com #查看这个域名的ns记录
dig a baidu.com #查看A记录
dig cname baidu.com
```

​    

## ip

```bash
ip addr #查看本机IP地址 和ifconfig功能一样

```

​    

## 文本处理

`Here文档`

```bash
#打印到屏幕上  等价于echo xxx | cat
$ cat << EOF #EOF可以是任意字符 用来代表开始结束标志
> aaa
> bbb
> ccc
> EOF

#重定向到文件
cat << EOF > ./a
> aaaaaaaaaaaaaaaaaaaa
> bbbbbbbbbbbbbbbbbbbb
> cccccccccccccccccccc
> EOF
```

`cat`

```bash
cat -n a.txt #输出带行号
cat -b a.txt #空行不编号
cat a.txt b.txt > c.out #合并文件
cat /dev/null > c.out #清空文件
cat > a.txt #键盘输入到文件 ctrl+d结束
```

`sort`

```bash
-f #不区分大小写
-r #逆序
-n #按照字符串长度排序(用于数字排序比较大小)
-t <char> #定义分割符
-k <n> #从第n列开始排序
-o <new-file> #将结果写入文件中 默认直接输出
```

```bash
sort id.txt #默认按照逐个比较ascii排序
sort a.txt b.txt > out.txt #排序并合并
sort -k2 -n  id.txt #不指定t则默认是以空格为分隔符
sort -t ':' -k2 -n  id.txt
```

`uniq`

```bash
uniq id.txt #去除重复的行
uniq -c id.txt #显示重复的次数
uniq -c id.txt | sort -nk1 #和sort配合 按重复行排序
```

`wc`

```bash
-l #行数
-w #单词数
-c #字符数
wc id.txt  #行数|单词数|字符数
```

`cut`

```bash
-d #定义分割符  默认是空格
-f #指定显示的列
-c #以字符为单位切割
-b #字节为单位切割
```

```bash
cut -d ':' -f1 id.txt #选每行第1列
cut -c1 id.txt #显示第1个字符
cut -c5- id.txt # [5,)
cut -c5-7 id.txt#显示 [5,7) 个字符
cut -c-7 #显示[1,7)
```

`head、tail`

```bash
head -2 id.txt #显示前2行
tail -2 id.txt  #显示后2行
```

​    

## sed和awk

`stream editor` sed流编辑器，核心是 **正则**

https://coolshell.cn/articles/9104.html

`awk` 流编辑器，的核心是**格式化**

https://coolshell.cn/articles/9070.html

https://www.ruanyifeng.com/blog/2018/11/awk.html

​    

## 用户和用户组管理

### 1、用户

用户添加

```bash
useradd abc
useradd abc -g g1 #指定组
useradd abc -G g1,g2 #指定多个组
```

用户删除

```bash
userdel abc #删除用户 保留家目录
userdel -r abc #删除用户 同时删除家目录
```

修改用户

```bash
#第一次创建用户必须设置密码  只有root才可以设置密码
passwd abc #不加用户名就是修改当前用户密码
passwd -l abc #锁定用户
passwd -u abc #解锁
passwd -d abc #设置无密码登入
passwd -f abc #强迫下次登入需要重新设置密码

#usermod和useradd用法一样
usermod -g g2 abc #指定abc的用户组为g2
usermod -aG docker $USER #将当前用户加入 docker组
```

用户登入注销

```bash
login abc
logout abc #exit也可
```

其他

```bash
id sys #查看用户的 group和uid等 不知道用户名则是当前用户
```

### 2、用户组

用户组的添加删除

```bash
groupadd g1
groupdel g1
```

group的修改

```bash
groupmod -n g2 g1 #将g1名字修改为g2
groupmod -g 111 lyer #将lyer组的gid修改为111
```

如果一个用户创建时没有指定组则会默认创建一个和用户名字一模一样的组

### 3、相关文件

`/etc/passwd` 记录用户账户信息 uid、gid、用户名、主目录...

```bash
#用户名:x占位符表示密码:uid:gid:家目录:用的shell
lyer:x:1000:1000::/home/lyer:/bin/bash
```

`/etc/shadow` 包含用户密码信息

```bash
#用户名:加密之后的密码:....一些标识信息
```

`/etc/group`  记录用户组，创建用户的时候linux给每个用户初始化一个组，gid和uid一样

```bash
#组名:x占位符表示密码:gid:组内成员uid(被隐藏,不可见)
lyer:x:1000:
```

### 4、常见问题

> 添加用户不会创建家目录

编辑`/etc/login.defs` 设置 `CREATE_HOME yes`

​    

## 时间相关

```bash
cal #显示当前月份的日历
cal 2020 #显示2020年的日历
cal 5 2020 #显示5月日历 可以查看星期几
date #显示当前时间
```

ubuntu可以通过`timedatectl`管理工具来设置时区等，具体查阅google

​    

## 其它

开关机

```bash
#-h:halt的意思
#shutdown会进行一些清理工作 然后调用init 0
shutdown -h now #立刻关机
shutdown -h 1 #1分钟之后 shutdown不加参数默认就是这个
shutdown -h 11:00 #11点关机
shutdown -r  now #立刻重启
#重启
reboot #init 6

sync #将内存数据同步到磁盘 关机前都要执行这个命令
last #查看关机和启动的日志
```

bash快捷键

```bash
ctrl+r #查找历史命令
```

​    

## 参考

https://coolshell.cn/articles/8883.html 【应该知道的LINUX技巧】

https://coolshell.cn/articles/7829.html 【28个UNIX/LINUX的命令行神器】