---
title: Pod总结
date: 2021-02-25
categories: [k8s]
tags: [k8s,云原生]
draft: true
---

## 命令行

```bash
kubectl get pod --my-namespace #指定namespace 默认是default
kubectl get pod/echo
```

`label`  注意label是不能加上空格等其他特殊字符的

```bash
kubcetl label pod nginx env=test
kubcetl label pod nginx env=test --overwrite #如果标签存在则覆盖
kubcetl label pod nginx env- #删除标签

kubectl get pod --show-labels -l app=echo,env=test #筛选 &关系
kubectl get pod --show-labels -l app!=echo

#in notin
kubectl get pod --show-labels -l 'env in (development,production)'
kubectl get pod --show-labels -l 'env notin (development,production)'
```

`annotate` 注解可以加上大块文本以及其他特殊字符 文本等，使用方法和label一样

```bash
kubectl annotate pod echo desc='hello  my name is echo pod!!!@@@#$'
#查看annotated只能查看pod的yaml
kubectl get pod nginx -o yaml
```

