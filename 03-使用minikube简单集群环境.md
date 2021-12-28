# 使用minikube搭建k8s集群

## 0. 系统要求
* 2 CPUs / 4GB Mems / 20GB Storage
* docker环境
* kubectl

## 1. 安装
rpm安装，版本1.24.0
```shell
$  curl -LO https://github.com/kubernetes/minikube/releases/download/v1.24.0/minikube-1.24.0-0.x86_64.rpm
$ rpm -Uvh  minikube-1.24.0-0.x86_64.rpm
Preparing...                          ################################# [100%]
Updating / installing...
   1:minikube-1.24.0-0                ################################# [100%]
$ minikube version
minikube version: v1.24.0
commit: 76b94fb3c4e8ac5062daf70d60cf03ddcc0a741b
```
## 2. 使用
### 2.1 启动
**普大喜奔，官方Minikube提供了完整对国内用户支持，完美支持Addon组件。**
建议参考：
- [https://yq.aliyun.com/articles/221687](https://yq.aliyun.com/articles/221687) 或 
- [https://github.com/AliyunContainerService/minikube/wiki](https://github.com/AliyunContainerService/minikube/wiki) 
- 最新支持minikube v1.24.0
```shell
$ minikube start --image-mirror-country='cn' --driver=docker --force
😄  minikube v1.24.0 on Centos 7.9.2009 (amd64)
❗  minikube skips various validations when --force is supplied; this may lead to unexpected behavior
✨  Using the docker driver based on user configuration
🛑  The "docker" driver should not be used with root privileges.
💡  If you are running minikube within a VM, consider using --driver=none:
📘    https://minikube.sigs.k8s.io/docs/reference/drivers/none/
✅  Using image repository registry.cn-hangzhou.aliyuncs.com/google_containers
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
    > registry.cn-hangzhou.aliyun...: 355.78 MiB / 355.78 MiB  100.00% 776.25 K
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🐳  Preparing Kubernetes v1.22.3 on Docker 20.10.8 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔎  Verifying Kubernetes components...
    ▪ Using image registry.cn-hangzhou.aliyuncs.com/google_containers/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
$ docker ps
CONTAINER ID   IMAGE                                                                 COMMAND                  CREATED         STATUS         PORTS                                                                                                                                  NAMES
319de37a2b89   registry.cn-hangzhou.aliyuncs.com/google_containers/kicbase:v0.0.28   "/usr/local/bin/entr…"   5 minutes ago   Up 5 minutes   127.0.0.1:49157->22/tcp, 127.0.0.1:49156->2376/tcp, 127.0.0.1:49155->5000/tcp, 127.0.0.1:49154->8443/tcp, 127.0.0.1:49153->32443/tcp   minikube
```
> 这里使用--force选项，--force=false: Force minikube to perform possibly dangerous operation
> 或者使用属于docker用户组的有sudo权限的非root账号进行操作

### 2.2 minikube相关操作
**显示状态**
```shell
$ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```
**显示插件列表**
```shell
$ minikube addons list
```
**启用dashboard插件**
```shell
$ minikube addons enable dashboard
```
**启动dashboard**
```shell
$ minikube dashboard
http://127.0.0.1:37137/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
```
**开启kubectl代理，集群外客户端可以访问dashboard**
```shell
$ kubectl proxy --port=8080 --address='172.31.1.5' --accept-hosts='^.*'
```
> 1. 云端服务器别忘了打开安全策略
> 2. Warning: 这样开启的dashboard没有开启token安全登录认证！！！
> 3. 访问连接：http://\<ip\>:8080/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/

### 2.3 简单的集群操作
**显示节点列表**
```shell
$ kubectl get nodes
NAME       STATUS   ROLES                  AGE    VERSION
minikube   Ready    control-plane,master   102m   v1.22.3
```
**显示kube-system名称空间下所有pod**
```shell
$ kubectl get pod -n kube-system
NAME                               READY   STATUS    RESTARTS       AGE
coredns-7d89d9b6b8-28dsf           1/1     Running   0              103m
etcd-minikube                      1/1     Running   0              103m
kube-apiserver-minikube            1/1     Running   0              103m
kube-controller-manager-minikube   1/1     Running   0              103m
kube-proxy-vf92m                   1/1     Running   0              103m
kube-scheduler-minikube            1/1     Running   0              103m
storage-provisioner                1/1     Running   1 (102m ago)   103m
```
**显示所有名称空间下所有service**
```shell
$ kubectl get svc -A
NAMESPACE              NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                  AGE
default                kubernetes                  ClusterIP   10.96.0.1       <none>        443/TCP                  123m
kube-system            kube-dns                    ClusterIP   10.96.0.10      <none>        53/UDP,53/TCP,9153/TCP   123m
kubernetes-dashboard   dashboard-metrics-scraper   ClusterIP   10.108.36.161   <none>        8000/TCP                 113m
kubernetes-dashboard   kubernetes-dashboard        NodePort    10.100.138.66   <none>        80:32551/TCP             113m
```

**最后**
这里不继续列举如何使用k8s集群，minikube可以快速搭建k8s集群，适用于开发环境或学习环境，生产或大型集群项目将采用其它方式搭建，后面会继续实践。