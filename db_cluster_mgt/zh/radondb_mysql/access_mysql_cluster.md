---
title: "访问 RadonDB MySQL"
keywords: 'KubeSphere, Kubernetes, 访问, RadonDB MySQL'
description: '了解如何从 KubeSphere 访问 RadonDB MySQL。'
linkTitle: "在 KubeSphere 中访问 RadonDB MySQL"
weight: 14293
---



本教程演示了如何在 KubeSphere 上访问 MySQL 数据库。

## 准备工作

- 请确保[已启用 OpenPitrix 系统](../../../pluggable-components/app-store/)。
- 您需要创建一个企业空间、一个项目和一个用户帐户 (`project-regular`) 供本教程操作使用。该帐户需要是平台普通用户，并邀请至项目中赋予 `operator` 角色作为项目操作员。本教程中，请以 `project-regular` 身份登录控制台，在企业空间 `demo-workspace` 中的 `demo-project` 项目中进行操作。有关更多信息，请参见[创建企业空间、项目、帐户和角色](../../../quick-start/create-workspace-and-project/)。
- 请确保 KubeSphere 项目网关已开启外网访问。有关更多信息，请参见[项目网关](https://kubesphere.io/zh/docs/project-administration/project-gateway/)。

## 通过内网访问

1. 打开**应用负载** > **服务**页面，点击 RadonDB MySQL 服务名称。

   ![RadonDB MySQL 服务](/images/docs/zh-cn/appstore/built-in-apps/radondb-mysql-app/radondb-mysql-service.png)

2. 在**容器组**下，展开菜单查看容器详情，然后点击**终端**图标。

   ![RadonDB MySQL 终端](/images/docs/zh-cn/appstore/built-in-apps/radondb-mysql-app/radondb-mysql-terminal.png)

3. 在弹出窗口中，直接向终端输入命令使用该应用。

   ![访问 RadonDB MySQL](/images/docs/zh-cn/appstore/built-in-apps/radondb-mysql-app/radondb-mysql-service-terminal.png)

## 通过外网访问


