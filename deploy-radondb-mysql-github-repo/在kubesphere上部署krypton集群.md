# **在 Kubesphere 上部署 Krypton 集群**

## **简介**

[KubeSphere](https://kubesphere.io) 是在 Kubernetes 之上构建的开源容器混合云，提供全栈的 IT 自动化运维的能力，简化企业的 DevOps 工作流。KubeSphere 提供了运维友好的向导式操作界面，帮助企业快速构建一个强大和功能丰富的容器云平台。

Krypton 是基于 MySQL 的开源、高可用、云原生集群解决方案。通过使用 Raft 协议，Krypton 可以快速进行故障转移，并且不会丢失任何事务。

## **部署准备**

### **安装 KubeSphere**

KubeSphere 提供了多种安装方式：

- 在 [青云公有云](https://appcenter.qingcloud.com/apps/app-cmgbd5k2) 上一键安装 Kubersphere。
- [在 Kubernetes 上安装 Kubersphere](https://kubesphere.io/zh/docs/installing-on-kubernetes/)。
- [在 Linux 上安装 Kubersphere](https://kubesphere.io/zh/docs/installing-on-linux/)。

### **创建企业空间、项目、帐户和角色**

参考 KubeSphere 官方文档：[创建企业空间、项目、帐户和角色](https://kubesphere.io/zh/docs/quick-start/create-workspace-and-project/)。

### **连接 KubeSphere 客户端节点**

以下示例适用于 KubeSphere 安装在 [青云公有云](https://appcenter.qingcloud.com/apps/app-cmgbd5k2) 。

直接使用 [青云控制台](https://console.qingcloud.com/) 连接 KubeSphere 客户端节点。

![连接客户端节点](png/连接客户端节点.png)

使用第三方 SSH 工具连接客户端节点，需要在 KubeSphere 配置参数中配置公钥。

![ssh公钥](png/ssh公钥.png)

## **部署步骤**

以下分别介绍使用 [命令行](#使用命令行部署-krypton集群) 和使用 [控制台](#使用控制台部署-krypton-集群) 两种部署方式在 KubeSphere 上部署 Krypton集群。

### **使用命令行部署 Krypton集群**

#### **步骤 1：拉取 Krypton Chart**

将 Krypton Chart 拉取到 KubeSphere 客户端节点中。

```bash
git clone https://github.com/zhyass/krypton-helm.git
```

Chart 代表着 [Helm](https://helm.sh/zh/docs/intro/using_helm/) 包。它包含在 Kubernetes 集群内部运行应用程序，工具或服务所需的所有资源定义。

#### **步骤 2：部署**

**默认部署**只需指定 release 名称，以下为指定 release 名为 `my-release` 的默认部署指令。

说明：release 是运行在 Kubernetes 集群中的 Chart 的实例。

```bash
## For Helm v2
cd charts
helm install . my-release

## For Helm v3
cd charts
helm install my-release .
```

**指定参数部署**可在 `helm install` 时使用 `--set key=value[,key=value]` 指令，例如，

```bash
cd charts
helm install my-release \
  --set mysql.mysqlUser=my-user,mysql.mysqlPassword=my-password,mysql.database=my-database .
```

以上指令创建了一个用户名为 `my-user` ，密码为 `my-password` 的标准数据库用户，可访问名为 `my-database` 的数据库。
当然，也可以通过 value.yaml 文件在安装时配置指定参数，例如，

```bash
cd charts
helm install my-release -f values.yaml .
```

更多配置参数，可参考 [配置](#配置) ，其中列出了所有可在安装过程中配置的参数。

默认部署指令执行成功后出现如下提示信息。
![部署成功](png/部署成功.png)

登录 KubeSphere 控制台，查看工作负载中的有状态副本集，Krypton 已经成功部署。
![控制台部署成功](png/控制台部署成功.png)

### **使用控制台部署 Krypton 集群**

#### **步骤 1：制作 Krypton Chart 压缩包**

拉取 Krypton Chart。

```bash
git clone https://github.com/zhyass/krypton-helm.git
```

打包生成 tgz 或 tar.gz 文件。

```bash
cd krypton-helm
helm package charts
```

![打包chart](png/打包chart.png)

#### **步骤 2：控制台部署**

登录 KubeSphere 的 Web 控制台，进入 **平台管理** 的 **访问控制** 界面。进入提前创建好的 `krypton-workspace` **企业空间。**

![企业空间](png/企业空间.png)

选择 **应用管理** 中的 **应用模板**。

![应用管理](png/应用管理.png)

选择 **创建** ，在弹窗中上传打包好的 Krypton Chart 文件。

![上传](png/上传.png)

选择 **项目管理**，进入提前创建好的 `krypton-deploy` 项目中，选择 **应用负载** 中的 **应用**。

![应用负载](png/应用负载.png)

选择 **部署新应用**，在弹窗中选择 **来自应用模板**，选择 `krypton1`。

![部署新应用](png/部署新应用.png)

编辑基本信息。

![基本信息](png/基本信息.png)

编辑应用配置，点击 **部署** 即可完成部署。

![应用配置](png/应用配置.png)

说明：示例使用默认参数部署。

查看 **应用负载** 中的 **工作负载**，选择 **有状态副本集**，release 名为 `my-release` 的 Krypton 集群已成功部署。

![控制台部署成功](png/控制台部署成功.png)

## **访问 Krypton 节点**

Krypton 由一个主节点和两个从节点组成，每个节点都可以通过如下方式访问：

```txt
<pod-name>.my-release-krypton1
```

## **连接数据库**

### **步骤 1：创建 Client**

创建一个用于连接 Krypton 集群的客户端主机。

```bash
kubectl run -i --tty ubuntu --image=ubuntu:focal --restart=Never -- bash -il
```

### **步骤 2：安装 mysql-client**

在客户端主机中安装 mysql-client。

```bash
apt-get update && apt-get install mysql-client -y
```

### **步骤 3：获取密码**

获取 MySQL 用户密码，默认用户名为 `qingcloud`，默认密码为 `Qing@123`。

```bash
kubectl get secret -n default my-release-krypton1 -o jsonpath="{.data.mysql-password}" | base64 --decode; echo
```

### **步骤 4：连接主节点**

使用默认用户名，密码连接主节点。

```bash
mysql -h my-release-krypton1-master -u qingcloud -p
```

### **步骤 5：连接从节点**

使用默认用户名，密码连接从节点。

```bash
mysql -h my-release-krypton1-slave -u qingcloud -pQing@123
```

说明：从节点为只读节点。

## **配置**

下表列出了 Krypton Chart 的配置参数及对应的默认值。

| 参数                                          | 描述                                                                                             |  默认值                                          |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------ | ----------------------------------------------- |
| `imagePullPolicy`                            | 镜像拉取策略                                                                                       | `IfNotPresent`                                  |
| `fullnameOverride`                           | 自定义全名覆盖                                                                                      |                                                 |
| `nameOverride`                               | 自定义名称覆盖                                                                                      |                                                 |
| `replicaCount`                               | Pod 数目                                                                                          | `3`                                             |
| `busybox.image`                              | `busybox` 镜像库地址                                                                               | `busybox`                                       |
| `busybox.tag`                                | `busybox` 镜像标签                                                                                 | `1.32`                                          |
| `mysql.image`                                | `mysql` 镜像库地址                                                                                 | `zhyass/percona57`                              |
| `mysql.tag`                                  | `mysql` 镜像标签                                                                                   | `beta0.1.0`                                     |
| `mysql.mysqlReplicationPassword`             | `qc_repl` 用户密码                                                                                 | `Repl_123`, 如果没有设置则随机12字符                |
| `mysql.mysqlUser`                            | 新建用户的用户名                                                                                    | `qingcloud`                                      |
| `mysql.mysqlPassword`                        | 新建用户的密码                                                                                      | `Qing@123`, 如果没有设置则随机12字符                |
| `mysql.mysqlDatabase`                        | 将要创建的数据库名                                                                                   | `qingcloud`                                     |
| `mysql.initTokudb`                           | 安装 tokudb 引擎                                                                                   | `false`                                         |
| `mysql.uuid`                                 | mysql 的 Server_uuid                                                                              | 由 `uuidv4` 函数生成                             |
| `mysql.args`                                 | 要传递到 mysql 容器的其他参数                                                                         | `[]`                                            |
| `mysql.livenessProbe.initialDelaySeconds`    | Pod 启动后首次进行存活检查的等待时间                                                                    | 30                                              |
| `mysql.livenessProbe.periodSeconds`          | 存活检查的间隔时间                                                                                    | 10                                              |
| `mysql.livenessProbe.timeoutSeconds`         | 存活探针执行检测请求后，等待响应的超时时间                                                                | 5                                               |
| `mysql.livenessProbe.successThreshold`       | 存活探针检测失败后认为成功的最小连接成功次数                                                              | 1                                               |
| `mysql.livenessProbe.failureThreshold`       | 存活探测失败的重试次数，重试一定次数后将认为容器不健康                                                      | 3                                               |
| `mysql.readinessProbe.initialDelaySeconds`   | Pod 启动后首次进行就绪检查的等待时间                                                                    | 10                                              |
| `mysql.readinessProbe.periodSeconds`         | 就绪检查的间隔时间                                                                                    | 10                                              |
| `mysql.readinessProbe.timeoutSeconds`        | 就绪探针执行检测请求后，等待响应的超时时间                                                                | 1                                               |
| `mysql.readinessProbe.successThreshold`      | 就绪探针检测失败后认为成功的最小连接成功次数                                                               | 1                                               |
| `mysql.readinessProbe.failureThreshold`      | 就绪探测失败的重试次数，重试一定次数后将认为容器未就绪                                                       | 3                                               |
| `mysql.extraEnvVars`                         | 其他作为字符串传递给 `tpl` 函数的环境变量                                                                |                                                 |
| `mysql.resources`                            | `MySQL` 的资源请求/限制                                                                               | 内存: `256Mi`, CPU: `100m`                    |
| `krypton.image`                              | `krypton` 镜像库地址                                                                                 | `zhyass/krypton`                                |
| `krypton.tag`                                | `krypton` 镜像标签                                                                                   | `beta0.1.0`                                     |
| `krypton.args`                               | 要传递到 krypton 容器的其他参数                                                                        | `[]`                                            |
| `krypton.extraEnvVars`                       | 其他作为字符串传递给 `tpl` 函数的环境变量                                                                 |                                                 |
| `krypton.livenessProbe.initialDelaySeconds`  | Pod 启动后首次进行存活检查的等待时间                                                                      | 30                                              |
| `krypton.livenessProbe.periodSeconds`        | 存活检查的间隔时间                                                                                     | 10                                              |
| `krypton.livenessProbe.timeoutSeconds`       | 存活探针执行检测请求后，等待响应的超时时间                                                                 | 5                                               |
| `krypton.livenessProbe.successThreshold`     | 存活探针检测失败后认为成功的最小连接成功次数                                                               | 1                                               |
| `krypton.livenessProbe.failureThreshold`     | 存活探测失败的重试次数，重试一定次数后将认为容器不健康                                                       | 3                                               |
| `krypton.readinessProbe.initialDelaySeconds` | Pod 启动后首次进行就绪检查的等待时间                                                                     | 10                                              |
| `krypton.readinessProbe.periodSeconds`       | 就绪检查的间隔时间                                                                                     | 10                                              |
| `krypton.readinessProbe.timeoutSeconds`      | 就绪探针执行检测请求后，等待响应的超时时间                                                                 | 1                                               |
| `krypton.readinessProbe.successThreshold`    | 就绪探针检测失败后认为成功的最小连接成功次数                                                                | 1                                               |
| `krypton.readinessProbe.failureThreshold`    | 就绪探测失败的重试次数，重试一定次数后将认为容器未就绪                                                       | 3                                               |
| `krypton.resources`                          | `krypton` 的资源请求/限制                                                                             | 内存: `128Mi`, CPU: `50m`                     |
| `metrics.enabled`                            | 以 side-car 模式开启 Prometheus Exporter                                                              | `true`                                          |
| `metrics.image`                              | Exporter 镜像地址                                                                                     | `prom/mysqld-exporter`                          |
| `metrics.tag`                                | Exporter 标签                                                                                        | `v0.12.1`                                       |
| `metrics.annotations`                        | Exporter 注释                                                                                        | `{}`                                            |
| `metrics.livenessProbe.initialDelaySeconds`  | Pod 启动后首次进行存活检查的等待时间                                                                     | 15                                              |
| `metrics.livenessProbe.timeoutSeconds`       | 存活探针执行检测请求后，等待响应的超时时间                                                                 | 5                                               |
| `metrics.readinessProbe.initialDelaySeconds` | Pod 启动后首次进行就绪检查的等待时间                                                                     | 5                                               |
| `metrics.readinessProbe.timeoutSeconds`      | 就绪探针执行检测请求后，等待响应的超时时间                                                                 | 1                                               |
| `metrics.resources`                          | Exporter 资源 请求/限制                                                                               | 内存: `32Mi`, CPU: `10m`                      |
| `service.annotations`                        | Kubernetes 服务注释                                                                                  | {}                                              |
| `service.type`                               | Kubernetes 服务类型                                                                                  | NodePort                                        |
| `service.loadBalancerIP`                     | 服务负载均衡器 IP                                                                                     | `""`                                            |
| `service.nodePort`                           | 服务节点端口                                                                                          | `""`                                            |
| `service.clusterIP`                          | 服务集群 IP                                                                                          | `""`                                            |
| `service.port`                               | 服务端口                                                                                             | `3306`                                          |
| `schedulerName`                              | Kubernetes scheduler 名称(不包括默认)                                                                 | `nil`                                           |
| `priorityClassName`                          | 设置 Pod 的 priorityClassName                                                                       | `{}`                                            |
| `statefulsetAnnotations`                     | StatefulSet 注释                                                                                    | `{}`                                            |
| `podAnnotations`                             | Pod 注释 map                                                                                        | `{}`                                            |
| `podLabels`                                  | Pod 标签 map                                                                                        | `{}`                                            |
| `persistence.enabled`                        | 创建一个卷存储数据                                                                                    | true                                            |
| `persistence.size`                           | PVC 容量                                                                                           | 10Gi                                            |
| `persistence.storageClass`                   | PVC 类型                                                                                           | nil                                             |
| `persistence.accessMode`                     | 访问模式                                                                                            | ReadWriteOnce                                   |
| `persistence.annotations`                    | PV 注解                                                                                            | {}                                              |

## 持久化  

[MySQL](https://hub.docker.com/repository/docker/zhyass/percona57) 镜像在容器路径 `/var/lib/mysql` 中存储 MySQL 数据和配置。
默认情况下，PersistentVolumeClaim 不可用，可以通过更改 values.yaml 文件来启用持久化，开启后 PersistentVolumeClaim 会被自动创建并挂载到目录中。

> *"当Pod分配给节点时，将首先创建一个emptyDir卷，只要该Pod在该节点上运行，该卷便存在。 当Pod从节点中删除时，emptyDir中的数据将被永久删除."*

**注意**：PersistentVolumeClaim 中可以使用不同特性的 PersistentVolume，其 IO 性能会影响数据库的初始化性能。所以当使用 PersistentVolumeClaim 启用持久化存储时，可能需要调整 livenessProbe.initialDelaySeconds 的值。数据库初始化的默认限制是60秒 (livenessProbe.initialDelaySeconds + livenessProbe.periodSeconds * livenessProbe.failureThreshold)。如果初始化时间超过限制，kubelet将重启数据库容器，数据库初始化被中断，会导致持久数据不可用。

## 自定义 MySQL 配置

在 `mysql.configFiles` 中添加/更改 MySQL 配置。

```yaml
  configFiles:
    node.cnf: |
      [mysqld]
      default_storage_engine=InnoDB
      max_connections=65535

      # custom mysql configuration.
      expire_logs_days=7
```

