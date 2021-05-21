---
title: github上的docker镜像仓库
date: 2021-05-21
categories: [折腾笔记]
tags: [docker,github]
---

## github镜像仓库

github上也提供了docker镜像仓库服务，分为两类:

- `docker.pkg.github.com`  仓库关联的镜像仓库，必须对应一个仓库
- `ghcr.io` 仓库无关的镜像仓库，和用户关联

tag格式分别为:

```bash
docker.pkg.github.com/OWNER/REPOSITORY/IMAGE_NAME:VERSION
```

```bash
ghcr.io/OWNER/IMAGE_NAME:VERSION
```

登入的密码就是github上创建的`TOKEN`，注意token必须有读写package的相关权限，我把token记录在了环境变量中，也可以记录在指定的文件里，将echo替换为cat即可

```bash
echo $GITHUB_DOCKER_IMAGE_TOKEN | docker login https://docker.pkg.github.com -u biningo --password-stdin
```

```bash
echo $GITHUB_DOCKER_IMAGE_TOKEN | docker login https://ghcr.io -u biningo --password-stdin
```

​    

## 参考

[Working with the Docker registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-docker-registry)

[Working with the Container registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)