---
title: "部署 RadonDB ClickHouse"
keywords: 'KubeSphere, Kubernetes, 安装, RadonDB ClickHouse'
description: '了解如何从 KubeSphere 应用商店部署 RadonDB ClickHouse。'
linkTitle: "在 KubeSphere 中部署 RadonDB ClickHouse"
weight: 14293
---

[ClickHouse](https://clickhouse.tech/) 是一款用于联机分析 (OLAP) 的列式数据库管理系统 (DBMS)。[RadonDB ClickHouse](https://github.com/radondb/radondb-clickhouse-kubernetes) 是一款深度定制的 ClickHouse 集群应用，完美保持了 ClickHouse 集群功能特性，并具备集群自动管理、集群数据重分布、高性能低成本等优势功能特性。

本教程演示了如何在 KubeSphere 上部署 ClickHouse 集群。

## 准备工作

- 请确保[已启用 OpenPitrix 系统](../../../pluggable-components/app-store/)。
- 您需要创建一个企业空间、一个项目和一个用户帐户 (`project-regular`) 供本教程操作使用。该帐户需要是平台普通用户，并邀请至项目中赋予 `operator` 角色作为项目操作员。本教程中，请以 `project-regular` 身份登录控制台，在企业空间 `demo-workspace` 中的 `demo-project` 项目中进行操作。有关更多信息，请参见[创建企业空间、项目、帐户和角色](../../../quick-start/create-workspace-and-project/)。

## 动手实验

1. 在 `demo-project` 项目的**概览**页面，点击左上角的**应用商店**。

2. 在 **RadonDB 数据库**中找到 ClickHouse，点击**应用信息**页面上的**部署**。

   ![应用商店中的 ClickHouse](/images/docs/zh-cn/appstore/built-in-apps/radondb-clickhouse-app/radondb-clickhouse-in-app-store.png)

   ![部署 RadonDB ClickHouse](/images/docs/zh-cn/appstore/built-in-apps/radondb-clickhouse-app/deploy-radondb-clickhouse.png)

3. 设置名称并选择应用版本。请确保将 RadonDB ClickHouse 部署在 `demo-project` 中，点击**下一步**。

   ![确认部署](/images/docs/zh-cn/appstore/built-in-apps/radondb-clickhouse-app/confirm-deployment.png)

4. 在**应用配置**页面，操作完成后，点击**部署**。

   ![设置应用配置](/images/docs/zh-cn/appstore/built-in-apps/radondb-clickhouse-app/set-app-configuration.png)

5. 进入 **RadonDB 数据库应用**页面，待 ClickHouse 集群状态为`运行中`时，则集群启动并正常运行。

   ![RadonDB ClickHouse 运行中](/images/docs/zh-cn/appstore/built-in-apps/radondb-clickhouse-app/radondb-clickhouse-running.png)
