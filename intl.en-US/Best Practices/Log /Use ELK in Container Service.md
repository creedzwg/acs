# Use ELK in Container Service {#concept_qrv_zr1_ydb .concept}

Background

## Logs are an important component of the IT system. {#section_bcc_1s1_ydb .section}

They record system events and the time when the events occur. We can troubleshoot system faults according to the logs and make statistical analysis.

Logs are usually stored in the local log files. To view logs, log on to the machine and filter keywords by using grep or other tools. However, when the application is deployed on multiple machines, viewing logs in this way is inconvenient. To locate the logs for a specific error, you have to log on to all the machines and filter files one after another. That is why concentrated log storage has emerged. All the logs are collected in Log Service and you can view and search for logs in Log Service.

In the Docker environment, concentrated log storage is even more important. Compared with the traditional operation and maintenance mode, Docker usually uses the orchestration system to manage containers. The mapping between container and host is not fixed and containers might be constantly migrated between hosts. You cannot view the logs by logging on to the machine and the concentrated log becomes the only choice.

Container Service integrates with Alibaba Cloud Log Service and automatically collects container logs to Log Service by using declarations. However, some users might prefer the This document introduces how to use ELK in Container Service. ELK \(Elasticsearch+ Logstash+ Kibana\) combination. This document introduces how to use ELK in Container Service.

## Overall structure {#section_ccc_1s1_ydb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7263/15681848355750_en-US.png)

An independent Logstash cluster must be deployed. Logsteins are heavy and resource-intensive, so they don't run logstroudsburg on every machine, not to mention every docker. To collect the container logs, syslog, Logspout, and filebeat are used. You might also use other collection methods.

To try to fit the actual scenario, two clusters are created here: one is the **testelk** cluster for deploying ELK, and the other is the **app** cluster for deploying applications.

## Procedure {#section_scv_js1_ydb .section}

**Note:** The clusters and Server Load Balancer instance created in this document must be in the same region.

**Step 1. Create a Server Load Balancer instance**

To enable other services to send logs to Logstash, create and configure a Server Load Balancer instance before configuring Logstash.

1.  Log on to the [Server Load Balancer console](https://partners-intl.console.aliyun.com/#/slbnext) before creating an application.
2.  Create a Server Load Balancer instance whose Instance type is **Internet**.
3.  Add 2 listeners for the created Server Load Balancer instance. The frontend and backend port mappings of the 2 listeners are 5000: 5000 and 5044: 5044 respectively, with no backend server added.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7263/15681848355752_en-US.png)


**Step 2. Deploy ELK**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). Create a cluster named **testelk**.

    For how to create a cluster, see [Create a cluster](../../../../reseller.en-US/User Guide/Clusters/Create a cluster.md#).

    **Note:** The cluster and the Server Load Balancer instance created in step 1 must be in the same region.

2.  Bind the Server Load Balancer instance created in step 1 to this cluster.

    On the Cluster List page, Click Bind Server Load Balancer. Select the created Server Load Balancer instance from the Server Load Balancer ID list and then click OK. click **Manage** at the right of **testelk**. Click **Load Balancer Settings** in the left-side navigation pane. \> Click Bind **Server Load Balancer**. Select the created Server Load Balancer instance from the Server Load Balancer ID list and then click **OK**.

3.  Deploy ELK by using the following orchestration template. In this example, an application named **elk** is created.

    For how to create an application by using an orchestration template, see [Create an application](../../../../reseller.en-US/User Guide/Applications/Create an application.md#).

    **Note:** Replace `${SLB_ID}` in the orchestration file with the ID of the Server Load Balancer instance created in step 1.

    ``` {#codeblock_7e7_ohx_xuf}
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
           aliyun.lb.port_5044: 'tcp://${SLB_ID}:5044' #Create a Server Load Balancer instance first.
           aliyun.lb.port_5000: 'tcp://${SLB_ID}:5000'
         links:
           - elasticsearch
    ```

    In this orchestration file, the official images are used for Elasticsearch and Kibana, with no changes made. Logstash needs a configuration file, so make an image on your own to include the configuration file. The image source codes can be found in [demo-logstash](https://github.com/AliyunContainerService/demo-logstash).

    The Logstash configuration file is as follows. This is a simple Logstash configuration. Two input formats, syslog and filebeats, are provided and their external ports are 5044 and 5000 respectively.

    ``` {#codeblock_g1m_sr9_91s}
    input {
         beats {
             port => 5044
             type => beats
    
         tcp {
             port => 5000
             type => syslog
    
    
     filter {
    
     output {
         elasticsearch { 
             hosts => ["elasticsearch:9200"]
    
         stdout { codec => rubydebug }
    					
    ```

4.  Configure the Kibana index.
    1.  Access Kibana.

        The URL can be found under the Routes tab of the application. On the Application List page, click the application name **elk**. Click the Routes tab and then click the route address to access Kibana.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7263/15681848355757_en-US.png)

    2.  Create an index.

        Configure the settings as per your needs and then click Create.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7263/15681848365758_en-US.png)


## Step 3. Collect logs {#section_ddc_1s1_ydb .section}

In Docker, the standard logs adopt Stdout file pointer. The following example first demonstrates how to collect Stdout to ELK. If you are using file logs, you can use filebeat directly. WordPress is used for the demonstration. The following is the orchestration template of WordPress. An application **wordpress** is created in another cluster.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). Create a cluster named **app**.

    For how to create a cluster, see [Create a cluster](../../../../reseller.en-US/User Guide/Clusters/Create a cluster.md#).

    **Note:** The cluster and the Server Load Balancer instance created in step 1 must be in the same region.

2.  Create the application **wordpress** by using the following orchestration template.

    **Note:** Replace `${SLB_IP}` in the orchestration file with the IP address of the Server Load Balancer instance created in step 1.

    ``` {#codeblock_tia_ehg_mn3}
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
           -MySQL: MySQL
         environment:
           - WORDPRESS_DB_PASSWORD=password
         logging:
           driver: syslog
           options:
             syslog-address: 'tcp://${SLB_IP}:5000'
    ```

    After the application is deployed successfully, click the application name wordpress on the Application List page. Click the Routes tab and then click the route address to access the WordPress application. click the application name **wordpress** on the Application List page. Click the **Routes** tab and then click the route address to access the WordPress application.

3.  On the Application List page, click the application name **elk**. Click the Routes tab and then click the route address

    to access Kibana and view the collected logs.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7263/15681848365759_en-US.png)


