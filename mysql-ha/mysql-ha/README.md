# MySQL - 单个Master, 多个Slaves

[MySQL](https://MySQL.org)是世界上最流行的数据库服务器之一，值得注意的用户包括维基百科、Facebook和谷歌。

## 介绍

这个chart包可在一个[Kubernetes](http://kubernetes.io)集群上通过使用包管理器发布一个master mysql和多个slave MySQL部署。很大程度上受这个[教程](https://kubernetes.io/docs/tutorials/stateful-application/run-replicated-stateful-application/)启发, 进一步的工作是“生产化”这个例子。

## 先决条件

- Kubernetes1.6以上
- 底层支持PV

## 安装Chart包

安装一个release名称叫做`my-release`的chart包:

```bash
$ helm install --name my-release incubator/mysqlha
```

这个命令将使用默认配置将MySQL部署到Kubernetes集群.本[配置](#configuration)部分节列出可以在安装期间配置的参数。

### 卸载

卸载或者删除一个实例名称叫做`my-release`的deployment:

```bash
$ helm delete my-release
```

## 配置

下表列出了MySQL chart的可配置参数及其默认值。

| Parameter                  | Description                         | Default                                |
| -----------------------    | ----------------------------------- | -------------------------------------- |
| `mysqlImage`               | `mysql`镜像和标签              | `mysql:5.7.13`                         |
| `xtraBackupImage`          | `xtrabackup`镜像和标签         | `gcr.io/google-samples/xtrabackup:1.0` |
| `replicaCount`             | MySQL实例数            | 3                                      |
| `mysqlRootPassword`        | `root`用户密码       | 随机生成                     |
| `mysqlUser`                | 要创建的新用户的名称     | 空                                  |
| `mysqlPassword`            | 新用户的密码          | 随机生成                     |
| `mysqlReplicationUser`     | 主从复制用户名称       | `repl`                                 |
| `mysqlReplicationPassword` | 主从复制用户密码      | 随机生成                     |
| `mysqlDatabase`            | 要创建的新数据库的名称  | 空                                  | 
| `persistence.enabled`      | 创建一个卷来存储数据       | true                                   | 
| `persistence.size`         | 持久卷大小     | 10Gi                                   |
| `persistence.storageClass` | 持久卷类型     | 空                                  |
| `persistence.accessModes`  | 持久卷访问模式      | `[ReadWriteOnce]`                      |
| `persistence.annotations`  | 持久卷注释       | `{}`                                   |
| `resources`                | 请求/极限的CPU/内存资源大小 | Memory: `128Mi`, CPU: `100m`           |

通过`helm install `加`--set key=value[,key=value]`声明每个参数，打个比方:

## 持久化

[MySQL](https://hub.docker.com/_/mysql/)的数据和配置存储在容器的`/var/lib/mysql`路径上。

默认持久化功能是打开的，持久卷将会被创建并且被挂载到对应目录，所以需要声明一个PV：

```
# https://kubernetes.io/docs/user-guide/persistent-volumes/#azure-disk
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: fast
provisioner: kubernetes.io/azure-disk
parameters:
  skuName: Premium_LRS
  location: westus
```

如果想要禁用该项功能你可以修改values.yaml来禁用持久化然后取之代之用emptyDir。
