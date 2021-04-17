---
title: github上的docker镜像仓库
date: 2021-03-30
categories: [github]
tags: [github,docker]
---

github上提供两种镜像仓库服务:

- 基于仓库和项目的专有docker仓库，和项目仓库关联，只有删除仓库才可以删除image
- 基于个人账户的独立的docker仓库，和任何项目无关，完全独立

```bash
ghcr.io #账户仓库
ghcr.io/USERNAME/IMAGE_NAME:VERSION #打标签案例

docker.pkg.github.com #具体项目仓库
docker.pkg.github.com/USERNAME/REPO_NAME/IMAGE_NAME:VERSION #打标签案例
```

使用github镜像仓库之前需要进行登入，登入的用户名就是github的用户名，密码就是github上创建的`TOKEN`，注意需要赋予package权限

```bash
echo $GITHUB_DOCKER_IMAGE_TOKEN | docker login ghcr.io -u biningo --password-stdin
```

​    

## 参考

[GitHub 镜像仓库服务 Ghcr 快速上手教程](https://www.hi-linux.com/posts/29574.html)

[Container guides for GitHub Packages](https://docs.github.com/en/packages/guides/container-guides-for-github-packages)【官方文档】