# MySQL

[MySQL](https://MySQL.org)是世界上最流行的数据库服务器之一，值得注意的用户包括维基百科、Facebook和谷歌。

## 介绍
这个安装包通过使用包管理器[Helm](https://helm.sh)在一个[集群](http://kubernetes.io)上部署一个deployment类型的单点MySQL。

## 先决条件

- Kubernetes1.6以上
- 底层支持PV（持久卷）

## 安装chart

安装一个实例名称叫做`my-release`的实例:

```bash
$ helm install --name my-release stable/mysql
```

这个命令将使用默认配置将MySQL部署到Kubernetes集群.本[配置](#configuration)部分节列出可以在安装期间配置的参数.

默认情况下，将为root用户生成一个随机密码,如果你想要设置成自己的密码请修改value.yaml文件中的mysqlRootPassword的值。

通过下面的命令你可以获得你的root密码，记得修改[YOUR_RELEASE_NAME]：
``` bash
printf $(printf '\%o' `kubectl get secret [YOUR_RELEASE_NAME]-mysql -o jsonpath="{.data.mysql-root-password[*]}"`)
```
> **小提示**: 使用`helm list`查看所有release

## 卸载安装包

卸载或者删除一个实例名称叫做`my-release`的deployment:

```bash
$ helm delete my-release
```

该命令将删除与chart关联的所有Kubernetes组件，并删除release。

## 配置

下表列出了MySQL chart的可配置参数及其默认值。

|                 参数                 |                  描述                  |            默认值            |
| ------------------------------------ | -------------------------------------- | ---------------------------- |
| `image`                              | `mysql`镜像仓库.                       | `mysql`                      |
| `imageTag`                           | `mysql`镜像标签.                       | `5.7.14`                     |
| `imagePullPolicy`                    | 镜像拉取策略                           | `IfNotPresent`               |
| `mysqlRootPassword`                  | `root`用户的密码                       | 随机10个字符                 |
| `mysqlUser`                          | 创建新用户的名称                       | 空                           |
| `mysqlPassword`                      | 新用户密码                             | 随机10个字符                 |
| `mysqlDatabase`                      | 新数据库的名称                         | 空                           |
| `livenessProbe.initialDelaySeconds`  | 健康检查初始化延迟秒数                 | 30                           |
| `livenessProbe.periodSeconds`        | 检查周期                               | 10                           |
| `livenessProbe.timeoutSeconds`       | 超时时间                               | 5                            |
| `livenessProbe.successThreshold`     | 在失败后被认为是成功的最小连续成功次数 | 1                            |
| `livenessProbe.failureThreshold`     | 在成功后被认为是成功的最小连续失败次数 | 3                            |
| `readinessProbe.initialDelaySeconds` | 读写检查初始化延迟秒数                 | 5                            |
| `readinessProbe.periodSeconds`       | 检查周期                               | 10                           |
| `readinessProbe.timeoutSeconds`      | 超时时间                               | 1                            |
| `readinessProbe.successThreshold`    | 在失败后被认为是成功的最小连续成功次数 | 1                            |
| `readinessProbe.failureThreshold`    | 在成功后被认为是成功的最小连续失败次数 | 3                            |
| `persistence.enabled`                | 是否创建一个卷来存储数据               | true                         |
| `persistence.size`                   | pvc容量大小                            | 8Gi RW                       |
| `nodeSelector`                       | 用户pod声明的节点标签                  | {}                           |
| `persistence.storageClass`           | pvc类型                                | 空  (使用alpha存储类注释)    |
| `persistence.accessMode`             | ReadWriteOnce或者ReadOnly              | ReadWriteOnce                |
| `persistence.existingClaim`          | 已有的持久卷名称                       | 空                           |
| `persistence.subPath`                | 卷挂载的子路径                         | 空                           |
| `resources`                          | CPU/内存 资源请求/极限                 | Memory: `256Mi`, CPU: `100m` |
| `configurationFiles`                 | mysql配置文件列表                      | 空                           |
| `ssl.enabled`                        | 为MySQL连接设置和使用SSL               | `false`                      |
| `ssl.secret`                         | 包含SSL证书的secret名称                | mysql-ssl-certs              |
| `ssl.certificates[0].name`           | 包含SSL证书的secret名称                | 空                           |
| `ssl.certificates[0].ca`             | CA证书                                 | 空                           |
| `ssl.certificates[0].cert`           | 服务器证书 (公钥)                     | 空                           |
| `ssl.certificates[0].key`            | 服务器密钥 (私钥)                      | 空                           |
| `imagePullSecrets`                   | 包含私有注册表凭证的secret资源的名称   | 空                           |
| `initializationFiles`                | 在容器启动后运行的SQL文件列表          | 空                           |

将上面的一些参数映射到env变量请参考[MySQL DockerHub image](https://hub.docker.com/_/mysql/).

通过`helm install `加`--set key=value[,key=value]`声明每个参数，打个比方:

```bash
$ helm install --name my-release \
  --set mysqlRootPassword=secretpassword,mysqlUser=my-user,mysqlPassword=my-password,mysqlDatabase=my-database \
    stable/mysql
```

上面的命令将MySQL`root`用户的密码设置为 `secretpassword`。此外，它还为能访问到数据库名称叫做`my-database`的用户创建一个标准的数据库用户名称叫做`my-user`并且密码设置为`my-password`。

或者在安装chart期间使用一个提供指定参数值的YAML文件，比如：

```bash
$ helm install --name my-release -f values.yaml stable/mysql
```

> **小提示**: 你可以使用默认的[values.yaml](values.yaml)

## 持久化

[MySQL](https://hub.docker.com/_/mysql/)的数据和配置存储在容器的`/var/lib/mysql`路径上。

默认情况下，会创建PVC并将其挂载到该目录中。如需禁用此功能，您可以更改values.yaml禁用持久性并使用emptyDir.

> *"当Pod被分配给节点时，首先创建emptyDir卷，并且只要该Pod在该节点上运行就存在，当出于某种原因从节点中删除Pod时，emptyDir中的数据将被永远删除。"*

## 自定义MySQL配置文件

[MySQL](https://hub.docker.com/_/mysql/)镜像接受自定义配置文件并且存储在`/etc/mysql/conf.d`路径上。如果你想要使用自定义配置文件, 你可以通过在configurationFiles属性上传递文件内容来创建其他配置文件. 注意，根据MySQL文档，只加载以`.cnf`结尾的文件.

```yaml
configurationFiles:
  mysql.cnf: |-
    [mysqld]
    skip-host-cache
    skip-name-resolve
    sql-mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
  mysql_custom.cnf: |-
    [mysqld]
```

## MySQL初始化文件

[MySQL](https://hub.docker.com/_/mysql/)镜像接受以*.sh、*.sql和*.sql.gz结尾的文件挂载到`/docker-entrypoint-initdb.d`. 
这些文件在容器初始化时运行一次，在容器重新启动后被忽略。如果想使用初始化脚本，您可以通过将文件内容传递给`initializationFiles`属性来创建初始化文件。 


```yaml
initializationFiles:
  first-db.sql: |-
    CREATE DATABASE IF NOT EXISTS first DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
  second-db.sql: |-
    CREATE DATABASE IF NOT EXISTS second DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
```

## SSL
该chart支持使用用户提供的TLS/SSL证书来配置MySQL的[加密连接](https://dev.mysql.com/doc/refman/5.7/en/encrypted-connections.html)。这是通过将所需的证书颁发机构文件、服务器公钥证书和服务器私钥存储为Kubernetes secret来实现的。此chart表的SSL选项支持以下用例:

* 用helm管理证书secrets
* 不用helm管理证书secrets

## 用helm管理证书secrets

用`ssl.certificates`来包含你的证书数据。比如：

```
ssl:
  enabled: false
  secret: mysql-ssl-certs
  certificates:
  - name: mysql-ssl-certs
    ca: |-
      -----BEGIN CERTIFICATE-----
      ...
      -----END CERTIFICATE-----
    cert: |-
      -----BEGIN CERTIFICATE-----
      ...
      -----END CERTIFICATE-----
    key: |-
      -----BEGIN RSA PRIVATE KEY-----
      ...
      -----END RSA PRIVATE KEY-----
```

> **注意**: 确保你的z证书数据在values文件中格式正确。

## 不用helm管理证书secrets

1. 在安装此chart之前，确保证书secret存在。
2. 在`ssl.secret`中设置证书secret的名称。
3. 确保`ssl.certificates`下面没有条目。

你可以执行以下的命令来手动创建证书secret：
```
kubectl create secret generic mysql-ssl-certs \
  --from-file=ca.pem=./ssl/certificate-authority.pem \
  --from-file=server-cert.pem=./ssl/server-public-key.pem \
  --from-file=server-key.pem=./ssl/server-private-key.pem
```
> **注意**: `ca.pem`、`server-cert.pem`和`server-key.pem` **必须**作为通用secret的key名称。
