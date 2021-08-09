---
title: "在 KubeSphere 中部署 MySQL"
keywords: 'KubeSphere, Kubernetes, 安装, MySQL'
description: '如何通过应用商店在 KubeSphere 中部署 MySQL'

link title: "在 KubeSphere 中部署 Krypton 集群"
weight: 14280
---
Krypton 是基于 MySQL 的开源、高可用、云原生集群解决方案。通过使用 Raft 协议，Krypton 可以快速进行故障转移，且不会丢失任何事务。

本教程演示如何在 KubeSphere 的应用商店部署 Krypton 集群。

## 准备工作

- 您需要[启用 OpenPitrix 系统](../../../pluggable-components/app-store/)。
- 您需要创建一个企业空间、一个项目和一个用户帐户 (`project-regular`)。该帐户必须是已邀请至项目的平台普通用户，并且在项目中的角色为 `operator`。在本教程中，您需要以 `project-regular` 用户登录，并在 `demo-workspace` 企业空间的 `demo-project` 项目中进行操作。有关更多信息，请参见[创建企业空间、项目、帐户和角色](../../../quick-start/create-workspace-and-project/)。

## 动手实验

### 步骤 1：部署 Krypton 集群

可选择**命令行**或**控制台**两种方式部署 Krypton 集群。

**通过命令行部署**

1. 克隆 Krypton Chart。
   将 Krypton Chart 克隆到 KubeSphere 客户端节点中。

    ```bash
    git clone https://github.com/zhyass/krypton-helm.git
    ```
    > Chart 代表 [Helm](https://helm.sh/zh/docs/intro/using_helm/) 包，包含在 Kubernetes 集群内部运行应用程序、工具或服务所需的所有资源定义。
2. 定义运行实例。
   以默认部署一个名为 `my-release` 的 Chart 实例为例。
   ```bash
    < For Helm v2 > 
    cd charts
    helm install . my-release

    < For Helm v3 >
    cd charts
    helm install my-release .
   ```
   在 `helm install` 时使用 `--set key=value[,key=value]` 指令，可指定访问数据库进行部署。
    ```bash
     cd charts
     helm install my-release \
     --set mysql.mysqlUser=my-user,mysql.mysqlPassword=my-password,mysql.database=my-database .
    ```
     >其中， `my-user` 为标准数据库用户名， `my-password` 为标准数据库用户秘密， `my-database` 为可访问数据库名。
   
   此外，在安装时通过**value.yaml**文件，配置指定参数。

    ```bash
     cd charts
     helm install my-release -f values.yaml .
    ```
3. 部署校验
   默认部署指令执行完成后，回显如下提示信息，则部署指令执行成功。
   ![部署成功](../png/部署成功.png)
   登录 KubeSphere 的 Web 控制台，在**工作负载**中选择**有状态副本集**页签，可查看到目标副本集，则 Krypton 集群部署成功。
   ![控制台部署成功](../png/控制台部署成功.png)

**通过控制台部署**
1. 创建 Krypton Chart 压缩包。
   克隆 Krypton Chart，并打包生成 tgz 或 tar.gz 文件。
   ```bash
    git clone https://github.com/zhyass/krypton-helm.git
   ```
   ```bash
    cd krypton-helm
    helm package charts
   ```
   ![打包chart](../png/打包chart.png)

2. 在控制台部署。
  （1）登录 KubeSphere 的 Web 控制台。
  （2）选择 **平台管理 > 访问控制** ，进入平台级的访问控制页面。
  （3）选择**企业空间**，并点击已创建的 `krypton-workspace` 企业空间名称，进入企业空间管理页面。
   ![企业空间](../png/企业空间.png)
  （4）上传压缩包。
    选择 **应用管理 > 应用模板**，进入应用模板配置页面。
   ![应用管理](../png/应用管理.png)
  点击 **创建** ，在弹窗中上传步骤一创建的 Krypton Chart 压缩包。
   ![上传](../png/上传.png)
  （5）部署新应用。
    点击 **项目管理**，进入已创建的 `krypton-deploy` 项目管理中心。
    选择 **应用负载 > 应用**，进入项目应用管理页面。
    ![应用负载](../png/应用负载.png)
    点击 **部署新应用**，在 **来自应用模板**窗口中选择 `krypton1`。
    ![部署新应用](../png/部署新应用.png)
    编辑应用模板基本信息。
    ![基本信息](../png/基本信息.png)
    编辑应用配置，点击**部署** 即可完成部署。
    ![应用配置](../png/应用配置.png)
    >说明：示例使用默认参数部署。

3. 部署校验
   在**项目管理**管理中心，选择 **应用负载 > 工作负载**，并选择**有状态副本集**页签，可查看到release 名为 `my-release` 的副本集，则 Krypton 集群已成功部署。
   ![控制台部署成功](../png/控制台部署成功.png)

### 步骤 2：访问 Krypton 节点

Krypton 由一个主节点和两个从节点组成，可通过如下命令访问每个Krypton节点。

```txt
<pod-name>.my-release-krypton1
```

### 步骤 3：连接 Krypton 节点

1. 创建 Client。
   创建一个用于连接 Krypton 集群的客户端主机。

   ```bash
    kubectl run -i --tty ubuntu --image=ubuntu:focal --restart=Never -- bash -il
   ```
2. 安装 mysql-client。
   在客户端主机中安装 mysql-client。
    ```bash
     apt-get update && apt-get install mysql-client -y
    ```
3. 获取密码。
    获取 MySQL 用户密码。一般默认用户名为 `qingcloud`，默认密码为 `Qing@123`。
    ```bash
     kubectl get secret -n default my-release-krypton1 -o jsonpath="{.data.mysql-password}" | base64 --decode; echo
    ```
4. 连接主节点。
    使用默认用户名连接主节点。
    ```bash
    mysql -h my-release-krypton1-master -u <用户名> -p
    ```
5. 连接从节点。
   使用默认用户名，密码连接从节点。
   ```bash
   mysql -h my-release-krypton1-slave -u <用户名> -p<密码>
   ```
   >说明：从节点为只读节点。

### 参考：配置说明

下表列出了 Krypton Chart 的配置参数及对应的默认值。

| 参数                                   | 描述                                                                                             |  默认值                                          |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------ | ----------------------------------------------- |
| `imagePullPolicy`                            | 镜像拉取策略                                                                                       | `IfNotPresent`                                  |
| `fullnameOverride`                           | 自定义全名覆盖                                                                                      |                                  -               |
| `nameOverride`                               | 自定义名称覆盖                                                                                      |                          -                       |
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
| `mysql.extraEnvVars`                         | 其他作为字符串传递给 `tpl` 函数的环境变量                                                                |         -                                        |
| `mysql.resources`                            | `MySQL` 的资源请求/限制                                                                               | 内存: `256Mi`, CPU: `100m`                    |
| `krypton.image`                              | `krypton` 镜像库地址                                                                                 | `zhyass/krypton`                                |
| `krypton.tag`                                | `krypton` 镜像标签                                                                                   | `beta0.1.0`                                     |
| `krypton.args`                               | 要传递到 krypton 容器的其他参数                                                                        | `[]`                                            |
| `krypton.extraEnvVars`                       | 其他作为字符串传递给 `tpl` 函数的环境变量                                                                 |                        -                         |
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
