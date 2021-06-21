---
vtitle: kubectl命令行总结
date: 2021-06-16
categories: [k8s]
tags: [k8s,云原生]
draft: true
---

## 查看pod和标签

```bash
kubectl get pod
kubectl get pod -A #查看所有名称空间的pod
kubectl get pod -w #观察变化
kubectl get pod/echo-ip-pod
kubectl get pod -o wide
kubectl get pod --show-labels
kubectl describe pod echo-ip-pod
kubectl get pod -n mynamespace
kubectl get pod echo-ip-pod -o yaml
```

删除pod

```bash
kubectl delete pod nginx-pod
kubectl delete pod --all #删除所有资源
```

编辑pod的yaml

```bash
kubectl edit pod nginx-ping
```

label

```bash
kubectl get pod/echo-ip 
kubectl get pod --show-labels -l app=echo,env=test #筛选 &关系
kubectl get pod --show-labels -l app!=echo
kubectl get pod --show-labels -l 'env in (development,production)'
kubectl get pod --show-labels -l 'env notin (development,production)'
```

```bash
kubcetl label pod nginx-pod env=test
kubcetl label pod nginx-pod env=test --overwrite #如果标签存在则覆盖
kubcetl label pod nginx-pod env- #删除标签
```

​    

## namespace

```bash
kubectl get ns
kubectl get ns mynamespace
kubectl describe ns
kubectl desctibe ns mynamespace
```

​    

## deployment

```bash
kubectl get deploy
```

扩缩容

```bash
kubectl scale deploy deploy-demo --replicas=10
```

滚动更新

```bash
kubectl apply -f rollout-demo.yaml --record #--record记录一些信息 不加也可
#查看历史版本
kubectl rollout  history deployment/nginx-deployment
#回滚到上一个版本
kubectl rollout  undo deployment/nginx-deployment
#回滚到指定版本
kubectl rollout undo  deployment/nginx-deployment --to-version=1
kubectl rollout pause deployment/nginx-deployment #暂停当前的滚动更新
kubectl rollout resume deployment/nginx-deployment #恢复暂停的滚动更新
kubectl rollout  status deployment/nginx-deployment #查看当前更新状态
```

​    

## 集群相关

查看集群信息

```bash
kubectl cluster-info
```

查看node节点信息

```bash
kubectl get nodes
kubectl get nodes -o wide
kubectl describe nodes
kubectl describe node n1
```

​     

## 日志

```bash
kubectl logs <pod_name>
kubectl logs <pod_name> -c <container_name>
```

​    

## 其他

查看有哪些version

```bash
kubectl api-versions #查看有哪些version
```

查看yaml清单编写帮助

```bash
kubectl explain pod #参看yaml编写指南
kubectl explain deployment.spec
```

查看全部资源

```bash
kubectl get all -n mynamespace
```

执行pod容器内的命令

```bash
kubectl exec -it nginx-ping-pod -- /bin/bash #pod只有一个容器的情况
kubectl exec -it nginx-ping-pod -c nginx-container -- /bin/bash #多容器pod
```

​    

## 资源名称一些缩写

| 资源类型                 | 缩写   | 描述 |
| ------------------------ | ------ | ---- |
| clusters                 |        |      |
| componentstatuses        | cs     |      |
| configmaps               | cm     |      |
| daemonsets               | ds     |      |
| deployments              | deploy |      |
| endpoints                | ep     |      |
| event                    | ev     |      |
| horizontalpodautoscalers | hpa    |      |
| ingresses                | ing    |      |
| jobs                     |        |      |
| limitranges              | limits |      |
| namespaces               | ns     |      |
| networkpolicies          |        |      |
| nodes                    | no     |      |
| statefulsets             |        |      |
| persistentvolumeclaims   | pvc    |      |
| persistentvolumes        | pv     |      |
| pods                     | po     |      |
| podsecuritypolicies      | psp    |      |
| podtemplates             |        |      |
| replicasets              | rs     |      |
| replicationcontrollers   | rc     |      |
| resourcequotas           | quota  |      |
| cronjob                  |        |      |
| secrets                  |        |      |
| serviceaccount           | sa     |      |
| services                 | svc    |      |