# 自定义路由-支持 TCP 协议 {#concept_pws_ywz_xdb .concept}

阿里云容器服务在使用的过程中，针对 TCP 负载均衡的场景，会遇到这样的问题：如果一个应用的客户端镜像和服务端镜像均部署在同一个节点（ECS）上面，由于受负载均衡的限制，该应用的客户端不能通过负载均衡访问本机的服务端。本文档以常用的基于 TCP 协议的 redis 为例，通过自定义路由[acs/proxy](intl.zh-CN/用户指南/服务发现和负载均衡/自定义路由-使用手册.md#section_it1_v2z_xdb) 来解决这一问题。

**Note:** 任何两个不同的服务均不能共享使用同一个负载均衡，否则会导致负载均衡后端机器被删除，服务不可用。

## 解法一：通过调度容器，避免客户端和服务端容器部署在同一个节点 {#section_udf_jxz_xdb .section}

示例应用模板（使用了[lb](intl.zh-CN/用户指南/服务编排/lb.md#) 标签和 [swarm filter](https://docs.docker.com/swarm/scheduler/filter/) 功能）：

```
redis-master:
    ports:
      - 6379:6379/tcp
    image:  'redis:alpine'
    labels:
        aliyun.lb.port_6379: tcp://proxy_test:6379
redis-client:
    image:  'redis:alpine'
    links:
      - redis-master
    environment: 
      - 'affinity:aliyun.lb.port_6379!=tcp://proxy_test:6379'
    command: redis-cli -h 120.25.131.64
    stdin_open: true
    tty: true
```

**Note:** 

-   如果发现调度不生效，在容器服务管理控制台，单击左侧导航栏的**服务**进入服务列表页面，选择您需要调度的服务，单击**重新调度** \> 在弹出的对话框中勾选**强制重新调度**，单击**确定**。
-   **强制重新调度**会丢弃已有容器的 volume，请做好相应的备份迁移工作。

## 解法二：容器集群内部客户端使用 link 访问服务端，集群外部使用负载均衡 {#section_c2f_jxz_xdb .section}

示例应用模板（使用了[lb](intl.zh-CN/用户指南/服务编排/lb.md#)标签）：

```
redis-master:
    ports:
      - 6379:6379/tcp
    image:  'redis:alpine'
    labels:
        aliyun.lb.port_6379: tcp://proxy_test:6379
redis-client:
    image:  'redis:alpine'
    links:
      - redis-master
    command: redis-cli -h redis-master
    stdin_open: true
    tty: true
```

## 解法三：容器集群内部客户端使用自定义路由（基于 HAProxy）作为代理访问服务端，集群外部使用负载均衡 {#section_j2f_jxz_xdb .section}

示例应用模板（使用了[lb](intl.zh-CN/用户指南/服务编排/lb.md#)标签和[自定义路由-简单示例](intl.zh-CN/用户指南/服务发现和负载均衡/自定义路由-简单示例.md#)）：

```
lb:
    image:  registry.aliyuncs.com/acs/proxy:0.6
    ports:
            -  '6379:6379/tcp'
    restart:  always
    labels:
        # addon 使得 proxy 镜像有订阅注册中心的能力，动态加载服务的路由
        aliyun.custom_addon:  "proxy"
        # 每台 vm 部署一个该镜像的容器
        aliyun.global:  "true"
        #  前端绑定负载均衡，使用 lb 标签
        aliyun.lb.port_6379: tcp://proxy_test:6379
        # 告诉系统，自定义路由需要等待 master 和 slave 启动之后再启动，并且对 master 和 slave 有依赖
        aliyun.depends: redis-master,redis-slave
    environment:
        #  支持加载路由的后端容器的范围，"*"表示整个集群，默认为应用内的服务
        ADDITIONAL_SERVICES:  "*"
        EXTRA_DEFAULT_SETTINGS: "log rsyslog local0,log global,option httplog"
        # 配置 HAProxy 工作于 tcp 模式
        MODE: "tcp"
    links:
        - rsyslog:rsyslog
rsyslog:
    image: registry.cn-hangzhou.aliyuncs.com/linhuatest/rsyslog:latest
redis-master:
    ports:
      - 6379/tcp
    image:  'redis:alpine'
    labels:
        # 告诉自定义路由需要暴露 6379 端口
        aliyun.proxy.TCP_PORTS:  "6379"
        # 告诉系统，该服务的路由需要添加到自定义路由服务中
        aliyun.proxy.required: "true"
redis-slave:
    ports:
      -  6379/tcp
    image:  'redis:alpine'
    links:
      - redis-master
    labels:
      # 告诉自定义路由需要暴露 6379 端口
      aliyun.proxy.TCP_PORTS:  "6379"
      # 告诉系统，该服务的路由需要添加到自定义路由服务中
      aliyun.proxy.required: "true"
      # 告诉系统，slave 需要等待 master 启动之后再启动，并且对 master 有依赖
      aliyun.depends: redis-master
    command: redis-server --slaveof redis-master 6379
redis-client:
    image:  'redis:alpine'
    links:
      - lb:www.example.com
    labels:
      aliyun.depends: lb
    command: redis-cli -h www.example.com
    stdin_open: true
    tty: true
```

该解决方案，做到了 redis 的主从架构，同时经过[服务间的负载均衡](intl.zh-CN/用户指南/服务发现和负载均衡/自定义路由-简单示例.md#section_wqf_v3z_xdb) 做负载均衡，做到了一定程度的高可用。

