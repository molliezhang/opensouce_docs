---
title: "查看集群监控"
keywords: 'KubeSphere, Kubernetes, 监控, RadonDB ClickHouse'
description: '了解如何从 KubeSphere 查看 RadonDB ClickHouse 集群监控。'
linkTitle: "在 KubeSphere 中查看 RadonDB ClickHouse 集群监控"
weight: 14293
---

通过 ClickHouse Operator 采集应用指标，并通过 Prometheus 进行查询，最终在 KubeSphere 监控面板实现监控指标可视化。

本教程演示如何从 KubeSphere 中查看 RadonDB ClickHouse 集群监控。

## 监控指标简介

### 资源监控指标

CPU 使用量  统计节点 CPU 资源使用量，单位为 m。
内存使用量 统计节点内存资源使用量，单位为 Mi。
网络流出速率 统计节点网络流出速率，单位为 Kbps。
网络流入速率 统计节点网络流入速率，单位为 Kbps。

### 服务监控指标

连接数   统计通过 TCP 或 HTTP 方式连接数据库的数量。
数据大小  统计数据库 MergeTree 表总容量，单位为 MB。
查询数    统计 SELECT 查询的总次数。
插入数据量  统计插入数据容量，单位为 KiB。
插入行数   统计插入行数量。
最大复制延迟  统计最大复制延迟时间，单位为秒。
Part 数   统计 MergeTree 表总 Part 数量。

## 准备工作

- 请确保[已启用 OpenPitrix 系统](../../../pluggable-components/app-store/)。
- 请确保创建一个企业空间、一个项目和一个用户帐户供本教程操作使用。有关更多信息，请参见[创建企业空间、项目、帐户和角色](../../../quick-start/create-workspace-and-project/)。
- 请确保已部署 RadonDB ClickHouse 集群。

## 查看节点服务监控

1. 选择**应用负载** > **应用**，点击**RadonDB 数据库应用**页面。

2. 在应用列表下，选择 ClickHouse 集群，进入集群详情页面。

3. 在**资源状态**页面**节点**列表区域，选择目标节点，点击监控图标，进入服务监控页面。

   ![服务监控](/images/docs/zh-cn/appstore/built-in-apps/radondb-clickhouse-app/service_monitoring_port.png)

4. 在时间过滤框，选择监控时间范围，点击**确定**，在页面即可查看指定时间范围的服务监控。

   ![服务监控](/images/docs/zh-cn/appstore/built-in-apps/radondb-clickhouse-app/service_monitoring.png)

## 查看节点资源监控

1. 选择**应用负载** > **应用**，点击**RadonDB 数据库应用**页面。

2. 在应用列表下，选择 ClickHouse 集群，进入集群详情页面。

3. 在**资源状态**页面**节点**列表区域，点击目标节点名称，进入节点容器组详情页面。

4. 选择**Monitoring**页签，进入资源监控页面

   ![资源监控](/images/docs/zh-cn/appstore/built-in-apps/radondb-clickhouse-app/resources_monitoring.png)

5. 在时间过滤框，选择监控时间范围，点击**确定**，在页面即可查看指定时间范围的资源监控。

   ![时间过滤](/images/docs/zh-cn/appstore/built-in-apps/radondb-clickhouse-app/resources_monitoring_timer.png)
