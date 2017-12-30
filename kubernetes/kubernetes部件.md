Kubernetes部件
=====

Master部件
----

### kube-apiserver

kube-apiserver 负责开放api。它是kubernetes控制的front end,可以垂直扩展。

### etcd
kubernetes 集群数据保存在etcd

### kube-controller-manager
kube-controller-manager运行各种运行任务的控制器。

这些控制器包括：

1. Node Controller: 负责监测节点的丢失
2. Replication Controller :负责维护正确数量pod
3. Endpoints Controller: 传送Endpoint对象
4. Service Account & Token Controllers: 为新的namespace创建用户和 api token

### cloud-controller-manager


### kube-scheduler
kube-scheduler为新创建的pod分配节点运行

### addons

### DNS

### Webui
一个ui界面观察kubernetes

### Container Resource Monitoring
记录容器时间序列metrics到数据库

### Cluster-level Logging
集群的日志

Node部件
-----
### kubelet
检查已经分配到节点的pods情况

1. 挂载pods需要的volumes
2. 下载pod需要的secret
3. 运行pod的容器在docker上
4. 周期性地检测容器存活
5. 报告pods的状态给其它系统

### kube-proxy
负责kubernete网络分发转发映射等功能

### docker
负责运行容器

### supervisord
负责监控kubelet和docker运行

### fluentd
一个守护进程提供cluster-level logging


