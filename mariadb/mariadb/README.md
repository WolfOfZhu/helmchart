# MariaDB

[MariaDB](https://mariadb.org)是世界上最流行的数据库之一，它是由MySQL的原开发人员制作的，并且保证保持开源，值得注意的用户包括维基百科、Facebook和谷歌。

MariaDB作为开源软件开发，作为关系数据库它提供了访问数据的SQL接口，MariaDB的最新版本还包括GIS和JSON特性。

## 安装

```bash
$ helm install stable/mariadb
```

## 介绍

这个安装包使用[Helm](https://helm.sh)包管理器在[Kubernetes](http://kubernetes.io)集群部署一个 具有主从复制的[MariaDB](https://github.com/bitnami/bitnami-docker-mariadb)statefulSet。

## 先决条件

- Kubernetes版本1.4以上
- 底层支持PV

## 安装Chart

安装一个实例名称叫做`my-release`的实例:

```bash
$ helm install --name my-release stable/mariadb
```

这个命令将使用默认配置将MariaDB部署到Kubernetes集群.本[配置](#configuration)部分节列出可以在安装期间配置的参数。

> **小提示**: 使用`helm list`查看所有release

## 卸载chart

卸载或者删除一个实例名称叫做`my-release`的statefulSet：

```bash
$ helm delete my-release
```

该命令将删除与chart关联的所有Kubernetes组件，并删除release。

## 配置

下表列出了MariaDB chart的可配置参数及其默认值。

|             Parameter                     |                     Description                     |                              Default                              |
|-------------------------------------------|-----------------------------------------------------|-------------------------------------------------------------------|
| `image.registry`                          | MariaDB镜像仓库地址                              | `docker.io`                                                       |
| `image.repository`                        | MariaDB镜像名称                                  | `bitnami/mariadb`                                                 |
| `image.tag`                               | MariaDB镜像标签                                   | `{VERSION}`                                                       |
| `image.pullPolicy`                        | MariaDB镜像拉取策略                           | `Always` if `imageTag` is `latest`, else `IfNotPresent`           |
| `image.pullSecrets`                       | 声明镜像拉取密钥                          | 空 (不要添加镜像拉取密钥到已经部署的pod里面)          |
| `service.type`                            | Kubernetes服务类型                             | `ClusterIP`                                                       |
| `service.port`                            | MySQL服务端口                                  | `3306`                                                             |
| `rootUser.password`                       | `root`用户密码                        | 随机10个字母数字组合长度的字符串                         |
| `rootUser.forcePassword`                  | 强制用户设置一个密码                   | `false`                                                           |
| `db.user`                                 | 创建新用户的名称                      | 空                                                             |
| `db.password`                             | 新用户的密码                           | 如果`db.user`已经定义了则设置为随机10个字母数字组合长度的字符串 |
| `db.name`                                 | 新数据库的名称                     | `my_database`                                                     |
| `replication.enabled`                     | 是否开启MariaDB的主从复制功能                         | `true`                                                             |
| `replication.user`                        | MariaDB主从复制用户名称                            | `replicator`                                                       |
| `replication.password`                    | MariaDB主从复制用户密码                   | 随机10个字母数字组合长度的字符串                         |
| `master.antiAffinity`                     | Master pod亲和性策略                     | `soft`                                                            |
| `master.persistence.enabled`              | 启用`PersistentVolumeClaim`持久卷  | `true`                                                            |
| `master.persistence.annotations`          | PVC注释                 | `{}`                                                              |
| `master.persistence.storageClass`         | 持久卷存储类                     | ``                                                                |
| `master.persistence.accessModes`          | 持久卷访问模式                      | `[ReadWriteOnce]`                                                 |
| `master.persistence.size`                 | 持久卷大小                              | `8Gi`                                                             |
| `master.config`                           | MariaDB主服务器的配置文件           | `默认值在values.yaml中`                        |
| `master.resources`                        | master节点的CPU/内存资源的请求值和极限值 | `{}`                                                              |
| `master.livenessProbe.enabled`            | 是否启用健康检查(master)             | `true`                                                            |
| `master.livenessProbe.initialDelaySeconds`| 健康检查初始化延迟秒数(master)   | `120`                                                             |
| `master.livenessProbe.periodSeconds`      | 检查周期(master)             | `10`                                                              |
| `master.livenessProbe.timeoutSeconds`     | 超时时间(master)                   | `1`                                                               |
| `master.livenessProbe.successThreshold`   | 在失败后被认为是成功的最小连续成功次数(master)| `1`                                                               |
| `master.livenessProbe.failureThreshold`   | 在成功后被认为是成功的最小连续失败次数(master) | `3`                                                               |
| `master.readinessProbe.enabled`           | 是否启用读写检查(master)            | `true`                                                            |
| `master.readinessProbe.initialDelaySeconds`| 读写检查初始化延迟秒数(master) | `15`                                                              |
| `master.readinessProbe.periodSeconds`     | 检查周期(master)             | `10`                                                              |
| `master.readinessProbe.timeoutSeconds`    | 超时时间(master)                   | `1`                                                               |
| `master.readinessProbe.successThreshold`  | 在失败后被认为是成功的最小连续成功次数(master)| `1`                                                               |
| `master.readinessProbe.failureThreshold`  | 在成功后被认为是成功的最小连续失败次数(master) | `3`                                                               |
| `slave.replicas`                          | slave数量                    | `1`                                                               |
| `slave.antiAffinity`                      | Slave pod 反关联性策略                      | `soft`                                                            |
| `slave.persistence.enabled`               | 启用`PersistentVolumeClaim`持久卷  | `true`                                                            |
| `slave.persistence.annotations`           | PVC注释                | `{}`                                                              |
| `slave.persistence.storageClass`          | 持久卷存储类                     | ``                                                                |
| `slave.persistence.accessModes`           | 持久卷访问模式                      | `[ReadWriteOnce]`                                                 |
| `slave.persistence.size`                  | 持久卷大小                              | `8Gi`                                                             |
| `slave.config`                            | MariaDB Slave的配置文件          | `_default values in the values.yaml file_`                        |
| `slave.resources`                         | slave节点的CPU/内存资源的请求值和极限值  | `{}`                                                              |
| `slave.livenessProbe.enabled`             | 是否启用健康检查(slave)              | `true`                                                            |
| `slave.livenessProbe.initialDelaySeconds` | 健康检查初始化延迟秒数(slave)    | `120`                                                             |
| `slave.livenessProbe.periodSeconds`       | 检查周期(slave)              | `10`                                                              |
| `slave.livenessProbe.timeoutSeconds`      | 超时时间(slave)                    | `1`                                                               |
| `slave.livenessProbe.successThreshold`    | 在失败后被认为是成功的最小连续成功次数(slave) | `1`                                                               |
| `slave.livenessProbe.failureThreshold`    | 在成功后被认为是成功的最小连续失败次数(slave)  | `3`                                                               |
| `slave.readinessProbe.enabled`            | 是否启用读写检查(slave)             | `true`                                                            |
| `slave.readinessProbe.initialDelaySeconds`| 读写检查初始化延迟秒数(slave)   | `15`                                                              |
| `slave.readinessProbe.periodSeconds`      | 检查周期(slave)              | `10`                                                              |
| `slave.readinessProbe.timeoutSeconds`     | 超时时间(slave)                    | `1`                                                               |
| `slave.readinessProbe.successThreshold`   | 在失败后被认为是成功的最小连续成功次数(slave) | `1`                                                               |
| `slave.readinessProbe.failureThreshold`   | 在成功后被认为是成功的最小连续失败次数(slave)  | `3`                                                               |
| `metrics.enabled`                         | 启动一个side-car prometheus exporter                | `false`                                                           |
| `metrics.image.registry`                           | Exporter镜像仓库地址                                 | `docker.io` | 
`metrics.image.repository`                           | Exporter镜像名称                                 | `prom/mysqld-exporter`                                            |
| `metrics.image.tag`                        | Exporter镜像标签                                  | `v0.10.0`                                                         |
| `metrics.image.pullPolicy`                 | Exporter镜像拉取策略                         | `IfNotPresent`                                                    |
| `metrics.resources`                       | Exporter资源请求值和极限值                    | 空                                                             |

上述参数映射到在[bitnami/mariadb](http://github.com/bitnami/bitnami-docker-mariadb)中定义的env变量。更多信息请参阅[bitnami/mariadb](http://github.com/bitnami/bitnami-docker-mariadb)中的镜像文档。

通过`helm install `加`--set key=value[,key=value]`声明每个参数，打个比方

```bash
$ helm install --name my-release \
  --set root.password=secretpassword,user.database=app_database \
    stable/mariadb
```

上述命令将MariaDB `root`账户的密码设置为`secretpassword`，此外他还创建了一个名称叫做`my_database`的数据库。

此外也可以在安装chart包期间使用yaml文件中的参数值，打个比方

```bash
$ helm install --name my-release -f values.yaml stable/mariadb
```

> **提示**: 你可以使用默认的[values.yaml](values.yaml)

## 持久化

[Bitnami MariaDB](https://github.com/bitnami/bitnami-docker-mariadb)镜像将MariaDB数据和配置存储在容器的`\bitnami`路径上。

该chart挂载了一个[持久卷](kubernetes.io/docs/user-guide/persistent-volumes/)在这个位置上。默认情况下使用c创建动态卷，你可以使用已有的PVC。
