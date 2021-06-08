---
title: git配置
date: 2021-06-05
categories: [git]
tags: [git]
---

## gitconfig配置文件

gitconfig配置文件优先级顺序如下

- `/etc/gitconfig`
- `~/.gitconfig`
- `.git/config`

​    

## gitconfig作用域

`global`全局作用域，修改的是`~/.gitconfig`

```bash
git config --global user.name lyer
```

`local` 本地作用域，修改的是`.git/config`

```bash
git config user.name lyer
```

​    

## 查看gitconfig

查看所有作用域下的gitconfig

```bash
git config --list
```

只查看`local`下的gitconfig

```bash
git config --list --local
```

只查看`global`下的gitconfig

```bash
git config --list --global
```

查看某个配置

```bash
git config user.email
```

​    

## 配置git命令别名

```bash
git config alias.st status #git st
```

