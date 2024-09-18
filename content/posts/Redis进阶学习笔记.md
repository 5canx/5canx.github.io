---
title: "Redis进阶学习笔记"
date: 2024-07-12T15:10:07+08:00
draft: true
description:
keywords:
license:
weight: 0
description: Redis主从复制、哨兵模式学习理解
tags:
  - Redis
categories:
  - Redis
---
<!--more-->

# 主从模式基本概念 

:one: **数据的复制是单向的，只能由主节点到从节点**

:two: **主从复制是Redis高可用的基础，还是哨兵和集群能够实施的基础**

:three: **主节点提供写服务，由从节点提供读服务（即写Redis数据时应用连接主节点，读Redis数据时应用连接从节点）**

:four: **在未开启哨兵模式下，如果从机断开重连，不会自动连接上主机**

:five: **从机只能读，不能写**

## 基本步骤

### 配置文件复制

cp redis_1.conf  redis_2.conf

### １．１修改从主机配置文件（redis2.conf）

```shell
port 1234
pid name
logfile
dump.rdb
```

### １．２启动并且查看运行情况

```shell
redis-server conf文件
ps -ef|grep redis
```



## 命令参数

```shell
info replication #查看当前Redis信息
slaveof 127.0.0.1 6379 # 确定这个库为主机
slaveof no one #让自己变成主机，但是从机需要手动连接到此主机
```

# 哨兵模式基本概念

:one:  哨兵是一个独立的 **进程** ，作为进程，它会独立运行。其原理是哨兵通过发送命令，等待Redis服务器响应，从而监控运行的多个Redis实例。

:two: 如果主机宕机，从机立刻选出一个主机，如果之前主机回来只能当从机，哨兵的默认配置时间时 30 秒。

## 基本步骤

### 添加哨兵配置文件  sentinel.conf   内容为：

```shell
#服务监听地址，用于客户端连接，默认本机地址
bind 0.0.0.0
#是否已后台daemon方式启动
daemonize yes
#安全保护模式
protected-mode no
#哨兵端口
port 26380
#日志文件路径
logfile "/myredis/sentinel26380.log"
#pid文件路径
pidfile /var/run/redis-sentinel26380.pid
#工作目录
dir /myredis
#设置要监控的master服务器，quorum表示最少有几个哨兵认可客观下线，同意故障迁移的法定票数
sentinel monitor mymaster 192.168.10.110 6379 2
#master设置了密码，连接master服务的密码
sentinel auth-pass mymaster 123456
```

### 启动哨兵

```shell
redis-sentinel sentinel.conf --sentinel &
```

### 故障转移

:information_source:  选主过程：

:one:  选择优先级最高的节点，通过sentinel配置文件中的replica-priority配置项，这个参数越小，表示优先级越高

:two:  如果第一步中的优先级相同，选择offset最大的，offset表示主节点向从节点同步数据的偏移量，越大表示同步的数据越多

:three:  如果第二步offset也相同，选择run id较小的

