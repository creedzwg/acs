# 容器服务中使用 ELK {#concept_qrv_zr1_ydb .concept}

本文档介绍如何在容器服务里使用 ELK。

## 背景信息 {#section_bcc_1s1_ydb .section}

日志是 IT 系统的重要组成部分，记录了系统在什么时候发生了什么事情。我们可以根据日志排查系统故障，也可以做统计分析。

通常日志存放在本机的日志文件里，需要查看日志的时候，登录到机器上，用 grep 等工具过滤关键字。但是当应用要部署在多台机器上的时候，这种方式查看日志就很不方便了，为了找到一个特定的错误对应的日志，不得不登录到所有的机器上，逐个过滤文件。于是出现了集中式的日志存储方式：所有日志收集到日志服务里，在日志服务里可以查看和搜索日志。

在 Docker 环境里，集中式日志存储更加重要。相比传统的运维模式，Docker 通常使用编排系统管理容器，容器和宿主机之间的映射并不固定，容器也可能不断的在宿主机之间迁移，登录到机器上查看日志的方式完全没法用了，集中式日志成了唯一的选择。

容器服务集成了阿里云日志服务，通过声明的方式自动收集容器日志到日志服务。但是有些用户可能更喜欢用 ELK（Elasticsearch＋Logstash＋Kibana）这个组合。本文档介绍如何在容器服务里使用 ELK。

## 整体结构 {#section_ccc_1s1_ydb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7263/15531566325750_zh-CN.png)

我们要部署一个独立的 logstash 集群。logstash 比较重，很耗资源，所以不会在每台机器上都运行 logstash，更不要说每个 Docker 容器里。为了采集容器日志，我们会用到 syslog、logspout 和 filebeat，当然您还可能会用到其他的采集方式。

为了尽可能贴合实际场景，这里我们创建两个集群：一个名为**testelk**的集群用来部署 ELK，一个名为 **app** 的集群用于部署应用。

## 操作步骤 {#section_scv_js1_ydb .section}

**Note:** 本文档中创建的集群和负载均衡均需位于同一地域下。

**步骤 1 创建负载均衡实例**

为了能让其他服务向 logstash 发送日志，我们需要在 logstash 前面配置负载均衡。

1.  创建应用前，登录 [负载均衡管理控制台](https://slbnew.console.aliyun.com/#/list/cn-hangzhou)。
2.  创建一个**公网类型**的负载均衡实例。
3.  设置两条监听规则。其中一条设置前端和后端的端口映射 5000：5000 ，另一条设置端口映射 5044：5044，不用添加后端服务器。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7263/15531566325752_zh-CN.png)


**步骤 2 部署 ELK**

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/#/overview/all)，创建集群 **testelk**。

    有关如何创建集群，参见[创建集群](../../../../../intl.zh-CN/用户指南/集群管理/创建集群.md#)。

    **Note:** 集群必须和上边创建的负载均衡实例位于同一地域。

2.  为集群绑定上边所创建的负载均衡实例。

    在集群列表页面，选择集群 **testelk**，单击右侧的**管理**，单击左侧导航栏中的**负载均衡** \> 单击**绑定SLB** \> 选择上边创建的负载均衡实例并单击**确定**。

3.  使用下面的编排模板部署 ELK。本示例创建了一个名为 **elk** 的应用。

    有关如何使用编排模板创建应用，参见 [创建应用](../../../../../intl.zh-CN/用户指南/应用管理/创建应用.md#)。

    **Note:** 您需要使用您上边所创建的负载均衡实例的 ID 替换编排文件中的 `${SLB_ID}`。

    ```
    version: '2'
     services:
       elasticsearch:
         image: elasticsearch
       kibana:
         image: kibana
         environment:
           ELASTICSEARCH_URL: http://elasticsearch:9200/
         labels:
           aliyun.routing.port_5601: kibana
         links:
           - elasticsearch
       logstash:
         image: registry.cn-hangzhou.aliyuncs.com/acs-sample/logstash
         hostname: logstash
         ports:
           - 5044:5044
           - 5000:5000
         labels:
           aliyun.lb.port_5044: 'tcp://${SLB_ID}:5044' #先创建slb
           aliyun.lb.port_5000: 'tcp://${SLB_ID}:5000'
         links:
           - elasticsearch
    ```

    在这个编排文件里，Elasticsearch 和 Kibana 我们直接使用了官方镜像，没有做任何更改。logstash 需要配置文件，需要自己做一个镜像，把配置文件放进去。镜像源码参见[demo-logstash](https://github.com/AliyunContainerService/demo-logstash)。

    logstash 的配置文件如下。这是一个非常简单的 logstash 配置，我们提供了 syslog 和 filebeats 两种输入格式，对外的端口分别是 5044 和 5000。

    ```
    input {
         beats {
             port => 5044
             type => beats
         }
         tcp {
             port => 5000
             type => syslog
         }
     }
     filter {
     }
     output {
         elasticsearch { 
             hosts => ["elasticsearch:9200"]
         }
         stdout { codec => rubydebug }
     }
    ```

4.  配置 kibana index。
    1.  访问 kibana。

        URL 可以在应用的路由列表（单击所创建的应用的名称 **elk**，单击路由列表页签 \> 单击路由地址）里找到。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7263/15531566325757_zh-CN.png)

    2.  创建 index。

        根据您的实际需求进行配置并单击**Create**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7263/15531566325758_zh-CN.png)


## 步骤 3 收集日志 {#section_ddc_1s1_ydb .section}

Docker 里标准的日志方式是用 Stdout，所以我们先演示如何把 Stdout 收集到 ELK。如果您在使用文件日志，可以直接用 filebeat。我们用 wordpress 作为演示用的例子，下面是 wordpress 的编排模板。我们在另外一个集群中创建应用 **wordpress**。

1.  登录 [容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs)，创建集群 **app**。

    有关如何创建集群，参见 [创建集群](../../../../../intl.zh-CN/用户指南/集群管理/创建集群.md#)。

    **Note:** 集群必须和上边创建的负载均衡实例位于同一地域。

2.  使用下面的编排模板创建应用**wordpress**。

    **Note:** 您需要使用您上边所创建的负载均衡实例的 IP 替换编排文件中的 `${SLB_IP}`。

    ```
    version: '2'
     services:
       mysql:
         image: mysql
         environment:
           - MYSQL_ROOT_PASSWORD=password
       wordpress:
         image: wordpress
         labels:
           aliyun.routing.port_80: wordpress
         links:
           - mysql:mysql
         environment:
           - WORDPRESS_DB_PASSWORD=password
         logging:
           driver: syslog
           options:
             syslog-address: 'tcp://${SLB_IP}:5000'
    ```

    待部署成功后，找到 wordpress 的访问地址（单击所创建的 **wordpress** 应用的名称 \> 单击**路由列表**页签 \> 单击路由地址），即可访问 wordpress 应用。

3.  在应用列表页面，单击应用**elk** \> 单击路由列表页签 \> 单击路由地址。

    成功访问 kibana 的页面，可以查看已经收集到的日志。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7263/15531566325759_zh-CN.png)


