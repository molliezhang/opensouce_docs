---
title: "访问 RadonDB PostgreSQL"
keywords: 'KubeSphere, Kubernetes, 访问, RadonDB PostgreSQL'
description: '了解如何从 KubeSphere 访问 RadonDB PostgreSQL。'
linkTitle: "在 KubeSphere 中访问 RadonDB PostgreSQL"
weight: 14293
---



本教程演示了如何在 KubeSphere 上访问 PostgreSQL 数据库。

## 准备工作

- 请确保[已启用 OpenPitrix 系统](../../../pluggable-components/app-store/)。
- 您需要创建一个企业空间、一个项目和一个用户帐户 (`project-regular`) 供本教程操作使用。该帐户需要是平台普通用户，并邀请至项目中赋予 `operator` 角色作为项目操作员。本教程中，请以 `project-regular` 身份登录控制台，在企业空间 `demo-workspace` 中的 `demo-project` 项目中进行操作。有关更多信息，请参见[创建企业空间、项目、帐户和角色](../../../quick-start/create-workspace-and-project/)。
- 请确保 KubeSphere 项目网关已开启外网访问。有关更多信息，请参见[项目网关](https://kubesphere.io/zh/docs/project-administration/project-gateway/)。

## 通过内网访问

1. 在 **应用负载**下的**容器组**页面，点击一个容器的名称，进入容器详情页面。

2. 在**资源状态**页面，点击**终端**图标。

   ![RadonDB PostgreSQL 终端](radondb-postgresql-app/radondb-postgresql-terminal.png)

3. 在弹出窗口中，向终端输入命令使用该应用。

   ```bash
   psql -h <Pod name> -p 5432 -U postgres -d postgres
   ```

   ![访问 RadonDB PostgreSQL](radondb-postgresql-app/radondb-postgresql-service-terminal.png)

## 通过外网访问


