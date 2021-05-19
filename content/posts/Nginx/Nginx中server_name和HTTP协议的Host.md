---
title: Nginx中server_name和HTTP协议中的Host
date: 2021-05-19
categories: [Nginx]
tags: [Nginx,HTTP,网络] 
---

## HTTP协议中的Host

HTTP请求头部有一个`Host`字段可以指定目标主机的域名或则主机名，但是最终域名不是需要转化为`IP:Port`吗？`IP:Port`不就是可以标识唯一应用了吗？ 那为啥HTTP请求头还需要一个`Host`字段来标识出域名呢？

这里涉及到 **虚拟主机** 的概念，即一台主机(一个IP) 可以拥有多个域名，每个域名都可以在`80`同端口下部署HTTP应用，客户端看访问应用则以为应用是一台独立的主机一样，但是实际上多个应用是共享同一台主机，只不过他们对应的域名不一样罢了，所以也叫虚拟主机

如果一个请求发送到服务器上的`80`端口，那么监听在`80`端口上的应用服务器会判断HTTP请求报文中的`Host`是不是自己的，如果是则接收并且响应，不是则丢弃。或则直接部署一个网关(Nginx)，网关捕获所有请求然后检查HTTP请求头的`Host`字段根据配置的 **Host规则** 转发到不同的后端服务器上即可

当然也可以将多个应用分别部署到不同的端口上，只是这样比较麻烦并且占用了端口，有时候还违背了一些默认规则，比如HTTP应用的默认端口80，如果我有两个网站那么我就不得不将其他网站的端口给修改了，这样会造成用户访问不方便

​    

## Nginx中的server_name

我们可以配置`server_name`规则来让Nginx检查`Host`字段并且转发到相应的服务器上

```nginx
server {
	listen 80;
	server_name www;
	location / {
		default_type text/html;
		content_by_lua '
			ngx.say("<p>www</p>")
		';
	}
}
 
server {
	listen  80;
	server_name www.lyer.com;
	location / {
		default_type text/html;
		content_by_lua '
			ngx.say("<p>www.lyer.com</p>")
		';        
	}
}
 
server {
	listen 80;
	server_name www.lyer.*;
	location / {
		default_type text/html;
		content_by_lua '
			ngx.say("<p>www.lyer.*</p>")
		';
 
	}
}
 
server {
	listen 80;
	server_name ~\w+.com;
	location / {
		default_type text/html;
		content_by_lua '
			ngx.say("<p>~\w+.com</p>")
		';        
	}
}
 
server {
	listen 80;
	server_name ~.*lyer.com;
	location / {
		default_type text/html;
		content_by_lua '
			ngx.say("<p>~.*lyer.com</p>")
		';
	}
}

#如果前面都匹配不到则转发到标注了default的server上
#如果没有default server则转发到第一个server上
server {
	listen 80 default;
	location / {
		default_type text/html;
		content_by_lua '
			ngx.say("<p>default</p>")
		';
	}
}
```

server_name与host匹配优先级如下：

- 完全匹配

- 通配符在前的，如*.test.com

- 通配符在后的，如www.test.*

- 正则匹配，如~^\.www\.test\.com$

如果都不匹配

- 优先选择listen配置项后有`default`或`default_server`的

- 找到匹配listen端口的第一个server块

​    

## 参考

[nginx配置:server_name的作用](https://blog.csdn.net/Cheng_Kohui/article/details/82930464)

