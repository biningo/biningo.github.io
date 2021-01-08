---
title: 容器导论
date: 2021-01-07
categories: [Docker和容器]
tags: [docker,容器]
---

  

## 挂载volume的行为

`volume`的挂载是 **以宿主机为主** ，主要有如下几个行为可能：

| host                          | container                            | mount result                                                 |
| ----------------------------- | ------------------------------------ | ------------------------------------------------------------ |
| 文件夹不存在/文件夹存在但为空 | 文件夹不存在/存在但为空/存在且不为空 | container中文件被覆盖（清空）                                |
| 文件夹存在且不为空            | 文件夹不存在/存在但为空/存在且不为空 | container中文件夹内容被覆盖（原内容清空， 覆盖为host上文件夹内容） |

​    

## 将容器已经存在的文件挂载到宿主机

由上面可知，容器中原本存在的文件一旦进过挂载，就一定会被宿主机覆盖，但是有如下几个方法，可以曲线救国：

- **docker cp命令先将容器内的数据copy到宿主机然后再进行挂载**
- 在`docker-entrypoint.sh`等脚本中执行创建文件和文件夹等相关命令，**因为挂载行为先与脚本的执行行为，所以在脚本执行的时候已经挂载volume，当脚本创建相关文件的时候就可以反映到宿主机上了**

  <br/>

## volume相关命令

```bash
docker volume ls #查看所有命名和匿名volume
docker inspect <volume-name> #查看volume相关信息

docker volume create <volume-name> #创建volume
docker volume rm <volume-name> #删除

docker volume preun #清空没有容器挂载中的volume stop中的容器的volume不会清空

#控制volume的读写权限
-v <xxx>:容器内路径:ro #只读 在容器内只能读挂载的文件 
-v <xxx>:容器内路径:rw  #读写 在容器内能读写挂载文件 默认
```

<br />

## volume权限问题

TODO

<br/>

## volume规则总结

- **应该避免挂载一个空的或未创建的目录，这样可能会清空容器中原来存在的文件**

- 如果挂载一个没有创建的文件，则docker会自动创建
- docker挂载路径容器和宿主机不支持 **相对路径**  ，都必须指定 **绝对路径**

<br/>

## 参考

https://www.ucloud.cn/yun/27376.html