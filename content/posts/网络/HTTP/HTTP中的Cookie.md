---
title: HTTP中的Cookie
date: 2021-05-31
categories: [网络]
tags: [HTTP,Cookie]
draft: true
---

## 为什么需要Cookie

HTTP协议是无状态的，每个HTTP请求都是独立的，但是我们可以通过Cookie在浏览器中保存一些用户的状态数据，下次请求时将这些Cookie通过Header携带给服务器，那么服务器就可以拿到这些保存在客户端中的Cookie数据了

Cookie是浏览器的特性，在移动端APP中使用HTTP协议请求后端服务就没有Cookie这一说法，移动端APP中不需要设置Cookie，但是移动端使用`SharedPreferences`来代替Cookie

​    

## Set-Cookie设置Cookie

服务器需要在响应报文头里面设置`Set-Cookie`字段，浏览器就会根据这个字段的信息来设置相关的Cookie数据保存在客户端本地

下次客户端请求的时候就会通过相关的条件比如请求的域名、URL、Cookie是否过期等条件过滤Cookie，将过滤出来的Cookie设置在Header中的`Cookie`字段，这样服务器就可以拿到Cookie了

`Set-Cookie`可以设置多个，代表设置多个Cookie数据

```http
HTTP/1.1 200 ok
...
Set-Cookie: username=lyer;Max-Age=1000
Set-Cookie: age=18;HttpOnly;Secure;Domain="www.baidu.com";Path=/;SameSite=Strict;Max-Age=1000
...
```

| Cookie属性 | 解释                                                         |
| ---------- | ------------------------------------------------------------ |
| `Expires`  | Cookie的失效的具体时间，在`HTTP1.1`中`expires` 被弃用，最好使用`Max-Age`来指定Cookie保存的时间秒数 |
| `Max-Age`  | 需要保存的秒数，设置为`<=0` 表示不保存，可以用此来主动让浏览器删除，优先级高于`Expires`，设置了此字段Expires就无效 |
| `Domain`   | 指定了哪些域名可以带上这个Cookie，域名需要在Header中的`Host`字段指定 |
| `Path`     | 指定路径前缀才可以带上Cookie，`\`则表示所有路径都可以使用    |
| `HttpOnly` | 此 Cookie 只能通过浏览器 HTTP请求进行传输，禁止其他方式访问，比如用`document.cookie`等js函数API来访问，防止XSS攻击 |
| `Secure`   | 此Cookie只能在HTTPS下才能使用                                |
| `SameSite` | 防止CSRF攻击，可以设置3个值: `Strict、Lax、None`             |

> **注意: Cookie的大小最大限制为4k**

`Session`也是通过Cookie来实现的，服务器会设置一个`SessionID`到Cookie中，这样没次客户端就会带上这个SessionID，服务器就可以根据SessionID再去数据库查询用户的状态信息，这样就实现免登入了

设置SessionID的过期时间可以实现用户的过期重登

​    

## 第三方Cookie

Cookie使用的时候还需要注意第三方Cookie，第一方和第三方的区别就是Cookie是否由网站自己设置

`www.google.com`为用户设置了第一方Cookie

```http
Set-Cookie: username=lyer;Max-Age=1000;Domain=www.google.com
```

现在在google的网站下有一张图片

```html
<img src="twitter.com" style="visibility:hidden;">
```

那么在加载google网站的时候都会加载这张图片，加载这张图片会请求src网址，然后响应就会设置属于twitter的Cookie，那么这个Cookie在google网站里就叫第三方Cookie，下次我们再请求这张图片时就会携带上这个第三方Cookie

```http
Set-Cookie: username=lyer;Max-Age=1000;Domain=twitter.com
```

第三方Cookie可以实现如下几个功能:

- **单点登入**

    比如你登入了淘宝，淘宝设置了一个天猫的cookie，那么用户访问天猫就不需要登入了

- **用户追踪**

    比如上面在google网站下的twitter的图片，用户访问google网站的时候就会访问到twitter并且会携带上属于twitter的Cookie，一旦twitter服务器收到了这个Cookie就可以知道某个用户访问了google网站

- **广告植入**

    当twitter知道了用户访问了哪些网站之后，便可以根据用户的喜好来给用户推荐一些广告

- **CSRF攻击**

    第三方网站可以引导用户点击连接，此连接会请求目标网站同时携带上目标网站设置的Cookie，那么第三方网站就可以通过验证了

​    

## SameSite防止CSRF攻击

SameSite可以设置为三个值:

- Strict
- Lax
- None

​    

## 参考

[Cookie 的 SameSite 属性](https://www.ruanyifeng.com/blog/2019/09/cookie-samesite.html)

