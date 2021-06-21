---
title: 打包一个最小的Go镜像
date: 2021-06-10
categories: [Docker和容器]
tags: [docker,Go]
---

> 此Dockerfile来源: https://github.com/new-go/port-scan

```dockerfile
FROM golang:1.16-alpine3.13 as builder 
ENV GOPROXY=https://goproxy.io
WORKDIR /build
ADD . /build/
RUN CGO_ENABLED=0 go build -a -ldflags "-s -w" -o pscan /build/

FROM scratch
COPY --from=builder /build/pscan /
ENTRYPOINT ["/pscan"]
```

`CGO_ENABLED=0`表示不使用cgo，可以编译为一个纯静态的二进制文件，这样就不需要依赖其他动态加载库了，方便部署到Docker中

`-ldflags`表示链接的一些参数，`-s` 的作用是去掉符号信息，`-w`的作用是去掉调试信息，这样可以最大程度减少镜像的体积

`-a` 强制重新构建

