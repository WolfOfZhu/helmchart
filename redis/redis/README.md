# Redis

[Redis](http://redis.io/) 是一个高级的键值缓存和存储。它通常被称为数据结构服务器，因为keys可以包含字符串、散列、列表、集合、排序集、位图和超重对数。

## TL;DR

```bash
# 测试配置
$ helm install stable/redis
```

```bash
# 生产配置
$ helm install stable/redis --values values-production.yaml
```

## 简介

这个chart包使用Helm包管理器(https://helm.sh)在一个Kubernetes集群(http://kubernetes.io)来引导一个Redis部署(https://github.com/bitnami/bitnami-docker-redis)。

## 先决条件

- Kubernetes 1.8+
- 底层基础设施中支持PV

## 安装Chart

安装一个发布名称为`my-release`的chart包:

```bash
$ helm install --name my-release stable/redis
```

这个命令在Kubernetes集群中部署Redis缺省配置。 该配置部分列出了在安装期间可以配置的参数。

> **提示**: 使用`helm list`列出所有版本

## 卸载chart

卸载名称为`my-release`的chart部署:

```bash
$ helm delete my-release
```

该命令删除与chart关联的所有Kubernetes组件并且删除发布。


## 配置
下表列出了Redis chart的可配置参数及其默认值。

|                    参数                     |                                描述                                |                   默认值                    |
| ------------------------------------------- | ------------------------------------------------------------------ | ------------------------------------------- |
| `image.registry`                            | Redis仓库地址                                                      | `docker.io`                                 |
| `image.repository`                          | Redis镜像名称                                                      | `bitnami/redis`                             |
| `image.tag`                                 | Redis Image tag                                                    | `{VERSION}`                                 |
| `image.pullPolicy`                          | Image镜像标签                                                      | `Always`                                    |
| `image.pullSecrets`                         | 将docker-ragistry的secret名称指定为数组                            | 空                                       |
| `cluster.enabled`                           | 使用主从拓扑                                                       | `true`                                      |
| `cluster.slaveCount`                        | slaves数量                                                         | 1                                           |
| `existingSecret`                            | 现有secret的名称 （为了提供密码验证）                              | 空                                       |
| `usePassword`                               | 使用密码                                                           | `true`                                      |
| `password`                                  | Redis密码 (如果存在secret集合则忽略）                              | Randomly generated                          |
| `networkPolicy.enabled`                     | 启用网络策略                                                       | `false`                                     |
| `networkPolicy.allowExternal`               | 连接不需要客户端标签                                               | `true`                                      |
| `serviceAccount.create`                     | 指定是否应该创建ServiceAccount                                     | `false`                                     |
| `serviceAccount.name`                       | 创建的ServiceAccount的名称                                         | Generated using the fullname template       |
| `rbac.create`                               | 指定是否应该创建RBAC资源                                           | `false`                                     |
| `rbac.role.rules`                           | 创建的规则                                                         | `[]`                                        |
| `metrics.enabled`                           | 启动一个a side-car prometheus exporter                             | `false`                                     |
| `metrics.image.registry`                    | Redis镜像仓库地址                                                  | `docker.io`                                 |
| `metrics.image.repository`                  | Redis镜像名称                                                      | `bitnami/redis`                             |
| `metrics.image.tag`                         | Redis镜像标签                                                      | `{VERSION}`                                 |
| `metrics.image.pullPolicy`                  | 镜像拉取策略                                                       | `IfNotPresent`                              |
| `metrics.image.pullSecrets`                 | 将docker-ragistry的secret名称指定为数组                            | 空                                       |
| `metrics.podLabels`                         | Metrics exporter pod的附加标签                                     | {}                                          |
| `metrics.podAnnotations`                    | for Metrics exporter pod的附加注解                                 | {}                                          |
| `master.service.type`                       | Kubernetes服务类型(redis metrics)                                  | `LoadBalancer`                              |
| `metrics.service.annotations`               | 要监视的服务的注解（redis主从服务)                                 | {}                                          |
| `metrics.service.loadBalancerIP`            | loadBalancerIP（如果redis metrics的服务类型是`LoadBalancer`）      | 空                                       |
| `metrics.resources`                         | 导出自愿请求量                                                     | Memory: `256Mi`, CPU: `100m`                |
| `persistence.existingClaim`                 | 提供一个已有的持久卷                                               | 空                                       |
| `master.persistence.enabled`                | 使用一个pvc来持久化数据（主节点）                                  | `true`                                      |
| `master.persistence.path`                   | 为了使用其他镜像而设置的卷的挂载路径                               | `/bitnami`                                  |
| `master.persistence.subPath`                | 卷挂载的子目录                                                     | `""`                                        |
| `master.persistence.storageClass`           | 备份pvc用的存储类                                                  | `generic`                                   |
| `master.persistence.accessModes`            | 持久卷访问模式                                                     | `[ReadWriteOnce]`                           |
| `master.persistence.size`                   | 数据卷的大小                                                       | `8Gi`                                       |
| `master.podLabels`                          | 主Redis的pod的附加标签                                             | {}                                          |
| `master.podAnnotations`                     | 主Redis的pod的附加注释                                             | {}                                          |
| `master.port`                               | 主Redis端口                                                        | 6379                                        |
| `master.args`                               | 主Redis命令行                                                    3 | []                                          |
| `master.disableCommands`                    | 禁用的redis命令（多个命令以逗号隔开）                              | `FLUSHDB,FLUSHALL`                          |
| `master.extraFlags`                         | 主Redis附加命令行                                                  | []                                          |
| `master.nodeSelector`                       | Redis主节点pod标签声明                                             | {"beta.kubernetes.io/arch": "amd64"}        |
| `master.tolerations`                        | Redis主节点容忍标签                                                | []                                          |
| `master.schedulerName`                      | 备用调度器名称                                                     | 空                                       |
| `master.service.type`                       | Kubernetes服务类型（主redis）                                      | `ClusterIP`                                 |
| `master.service.annotations`                | redis主服务的注释                                                  | {}                                          |
| `master.service.loadBalancerIP`             | 如果redis主服务类型是`LoadBalancer`，则声明loadBalancerIP          | 空                                       |
| `master.securityContext.enabled`            | 启用安全上下文（redis主pod）                                       | `true`                                      |
| `master.securityContext.fsGroup`            | 容器的组ID（redis主pod）                                           | `1001`                                      |
| `master.securityContext.runAsUser`          | 容器的用户IDredis主pod                                             | `1001`                                      |
| `master.resources`                          | 主Redis请求/限制的CPU/内容资源                                     | Memory: `256Mi`, CPU: `100m`                |
| `master.livenessProbe.enabled`              | 是否启用健康检查（redis主pod）                                     | `true`                                      |
| `master.livenessProbe.initialDelaySeconds`  | 健康检查初始化延迟秒数（redis主pod）                               | `30`                                        |
| `master.livenessProbe.periodSeconds`        | 检查频率（redis主pod）                                             | `30`                                        |
| `master.livenessProbe.timeoutSeconds`       | 检查超时秒数（redis主pod）                                         | `5`                                         |
| `master.livenessProbe.successThreshold`     | 失败后检查定义为成功标志的成功次数（redis主pod）                   | `1`                                         |
| `master.livenessProbe.failureThreshold`     | 成功后被定义为失败标志的最小失败次数                               | `5`                                         |
| `master.readinessProbe.enabled`             | 是否启用读写检查（redis主pod）                                     | `true`                                      |
| `master.readinessProbe.initialDelaySeconds` | 读写检查初始化延迟秒数（redis主pod）                               | `5`                                         |
| `master.readinessProbe.periodSeconds`       | 检查频率（redis主pod）                                             | `10`                                        |
| `master.readinessProbe.timeoutSeconds`      | 检查超时秒数（redis主pod）                                         | `1`                                         |
| `master.readinessProbe.successThreshold`    | 失败后检查定义为成功标志的成功次数（redis主pod）                   | `1`                                         |
| `master.readinessProbe.failureThreshold`    | 成功后被定义为失败标志的最小失败次数                               | `5`                                         |
| `slave.serviceType`                         | Kubernetes服务类型（从redis）                                      | `LoadBalancer`                              |
| `slave.service.annotations`                 | redis从服务的注释                                                  | {}                                          |
| `slave.service.loadBalancerIP`              | 如果redis从服务类型是`LoadBalancer`，则声明loadBalancerIP          | 空                                       |
| `slave.port`                                | 从Redis端口                                                        | `master.port`                               |
| `slave.args`                                | 从Redis命令行                                                      | `master.args`                               |
| `slave.disableCommands`                     | 禁用的redis命令（多个命令以逗号隔开）                              | `master.disableCommands`                    |
| `slave.extraFlags`                          | 主Redis附加命令行                                                  | `master.extraFlags`                         |
| `slave.livenessProbe.enabled`               | 是否启用健康检查（redis从pod）                                     | `master.livenessProbe.enabled`              |
| `slave.livenessProbe.initialDelaySeconds`   | 健康检查初始化延迟秒数（redis从pod）                               | `master.livenessProbe.initialDelaySeconds`  |
| `slave.livenessProbe.periodSeconds`         | 检查频率（redis从pod）                                             | `master.livenessProbe.periodSeconds`        |
| `slave.livenessProbe.timeoutSeconds`        | 检查超时秒数（redis从pod）                                         | `master.livenessProbe.timeoutSeconds`       |
| `slave.livenessProbe.successThreshold`      | 失败后检查定义为成功标志的成功次数（redis主pod）                   | `master.livenessProbe.successThreshold`     |
| `slave.livenessProbe.failureThreshold`      | 成功后被定义为失败标志的最小失败次数                               | `master.livenessProbe.failureThreshold`     |
| `slave.readinessProbe.enabled`              | 是否启用读写检查（redis从pod）                                     | `master.readinessProbe.enabled`             |
| `slave.readinessProbe.initialDelaySeconds`  | 读写检查初始化延迟秒数（redis从pod）                               | `master.readinessProbe.initialDelaySeconds` |
| `slave.readinessProbe.periodSeconds`        | 检查频率（redis从pod）                                             | `master.readinessProbe.periodSeconds`       |
| `slave.readinessProbe.timeoutSeconds`       | 检查超时秒数（redis从pod）                                         | `master.readinessProbe.timeoutSeconds`      |
| `slave.readinessProbe.successThreshold`     | 失败后检查定义为成功标志的成功次数（redis主pod）                   | `master.readinessProbe.successThreshold`    |
| `slave.readinessProbe.failureThreshold`     | 成功后被定义为失败标志的最小失败次数 (redis slave pod)             | `master.readinessProbe.failureThreshold`    |
| `slave.podLabels`                           | 主Redis的pod的附加标签                                             | `master.podLabels`                          |
| `slave.podAnnotations`                      | 主Redispod的附加注解                                               | `master.podAnnotations`                     |
| `slave.schedulerName`                       | 备用调度器的名称                                     | 空                                       |
| `slave.securityContext.enabled`             | 启用安全上下文(redis slave pod)                          | `master.securityContext.enabled`            |
| `slave.securityContext.fsGroup`             | 容器的组ID(redis slave pod)                       | `master.securityContext.fsGroup`            |
| `slave.securityContext.runAsUser`           | 容器的用户ID(redis slave pod)                        | `master.securityContext.runAsUser`          |
| `slave.resources`                           | Redis slave的CPU/内存资源请求/极限                    | `master.resources`                          |
| `slave.affinity`                            | 是否启用node/pod亲和性                                | {}                                          |

上述参数映射到在[bitnami/redis](http://github.com/bitnami/bitnami-docker-redis)中定义的env变量。更多信息请参阅[bitnami/redis](http://github.com/bitnami/bitnami-docker-redis)的镜像文档。

通过`helm install `加`--set key=value[,key=value]`声明每个参数，打个比方:

```bash
$ helm install --name my-release \
  --set password=secretpassword \
    stable/redis
```

上面的命令把redis服务器的密码设置为`secretpassword`.

此外也可以在安装chart包期间可以使用yaml文件中的参数值，打个比方：

```bash
$ helm install --name my-release -f values.yaml stable/redis
```

> **提示**: 你可以使用默认的[values.yaml](values.yaml)

> **minikube用户请注意**: minikube(版本v0.24.1)提供的持久卷的`hostPath`属性只能由root用户来定义。当Redis pod试图写入`bitnami`目录时，使用默认的chart会导致pod出错。请考虑在安装redis的时候加上`--set persistence.enabled=false`参数。更多信息请参阅minikube问题[1990](https://github.com/kubernetes/minikube/issues/1990)。

## NetworkPolicy

如果要启用redis的网络策略请安装[实现Kubernetes网络策略规范的网络插件](https://kubernetes.io/docs/tasks/administer-cluster/declare-network-policy#before-you-begin),
然后把`networkPolicy.enabled`设置为`true`.

对于Kubernetes v1.5和v1.6，你还必须通过设置DefaultDeny名称空间注释来打开NetworkPolicy。注意: 这将对命名空间中的所有pod执行策略:

    kubectl annotate namespace default "net.beta.kubernetes.io/network-policy={\"ingress\":{\"isolation\":\"DefaultDeny\"}}"

一旦NetworkPolicy启用, 只有带有生成的客户端标签的pods才能连接到Redis。安装成功后，该标签将显示在输出中。

## 持久化

[Bitnami Redis](https://github.com/bitnami/bitnami-docker-redis)镜像将Redis数据和配置存储在容器的`\bitnami`路径上.

默认情况下，chart在此位置挂载一个[持久卷](http://kubernetes.io/docs/user-guide/persistent-volumes/)然后使用动态卷配置创建卷。如果一个PVC已经存在，请在安装期间指定它。

默认情况下，chart会同时保存数据和配置. 如果你希望目录只保存数据请设置`persistence.path`到 `/bitnami/redis/data`和`persistence.subPath`到`redis/data`。

### 已有的PVC

1. 创建持久卷
1. 创建PVC
1. 安装chart包

```bash
$ helm install --set persistence.existingClaim=PVC_NAME stable/redis
```

## Metrics

该chart可选地启动一个metrics导出[prometheus](https://prometheus.io)。metrics endpoint (port 9121)暴露在该服务中。可以使用[Prometheus获取配置](https://github.com/prometheus/prometheus/blob/master/documentation/examples/prometheus-kubernetes.yml)中描述的内容从集群内部获取metrics，如果metrics可以从集群外部获取, 可以使用Kubernetes API代理访问endpoint。
