# Custom routing - Supports TCP {#concept_pws_ywz_xdb .concept}

When Alibaba Cloud Container Service is in use, the following problem may occur to TCP Server Load Balancer: when the client image and server image of an application are deployed on the same Elastic Compute Service \(ECS\) instance, the application client cannot access the local server by using Server Load Balancer due to the limitation of Server Load Balancer. In this document, take the common TCP-based Redis as an example[EN-US\_TP\_7111.md\#section\_it1\_v2z\_xdb](reseller.en-US/User Guide/Service discovery and load balancing/Custom routing - User guide.md#section_it1_v2z_xdb) to describe how to solve the problem by using the custom routing acs/proxy.

**Note:** Different services cannot share the same Server Load Balancer instance. Otherwise, the backend machine of the Server Load Balancer is deleted and the services are unavailable.

## Solution 1: Deploy client and server containers on different nodes by scheduling containers {#section_udf_jxz_xdb .section}

The following is a sample application template \(the [EN-US\_TP\_7033.md\#](reseller.en-US/User Guide/Service orchestrations/lb.md#) label and [swarm filter](https://docs.docker.com/swarm/scheduler/filter/) function are used\):

``` {#codeblock_s12_79w_z7a}
redis-master:
    ports:
      - 6379:6379/tcp
    image: 'redis:alpine'
    labels:
        aliyun.lb.port_6379: tcp://proxy_test:6379
redis-client:
    image: 'redis:alpine'
    links:
      - redis-master
    environment: 
      - 'affinity:aliyun.lb.port_6379! =tcp://proxy_test:6379'
    command: redis-cli -h 120.25.131.64
    stdin_open: true
    tty: true
```

**Note:** 

-   Follow these steps if the scheduling does not take effect: Log on to the Container Service console. Click Swarm \> **Services** in the left-side navigation pane. Select the cluster in which the service you want to reschedule resides from the Cluster drop-down list. Click **Reschedule** at the right of the service you want to reschedule. \> Select the **Force Reschedule** check box in the displayed dialog box and then click **OK**.
-   The volumes of existing containers will be lost if you select the **Force Reschedule** check box. Backup and migrate the data in advance.

## Solution 2: Clients inside the container cluster access the server by using links, while clients outside access the server by using Server Load Balancer {#section_c2f_jxz_xdb .section}

The following is a sample application template \(the [EN-US\_TP\_7033.md\#](reseller.en-US/User Guide/Service orchestrations/lb.md#) label is used\):

``` {#codeblock_0rk_8e0_r1h}
redis-master:
    ports:
      - 6379:6379/tcp
    image: 'redis:alpine'
    labels:
        aliyun.lb.port_6379: tcp://proxy_test:6379
redis-client:
    image: 'redis:alpine'
    links:
      - redis-master
    command: redis-cli -h redis-master
    stdin_open: true
    tty: true
```

## Solution 3: Clients inside the container cluster access the server by using Custom routing \(which is based on HAProxy and serves as a proxy server\), while clients outside access the server by using Server Load Balancer {#section_j2f_jxz_xdb .section}

The following is a sample application template \(the [EN-US\_TP\_7033.md\#](reseller.en-US/User Guide/Service orchestrations/lb.md#) label and [EN-US\_TP\_7112.md\#](reseller.en-US/User Guide/Service discovery and load balancing/Custom routing - simple sample.md#) are used\):

``` {#codeblock_h38_yno_thd}
lb:
    image: registry.aliyuncs.com/acs/proxy:0.5
    ports:
            - '6379:6379/tcp'
    restart: always
    labels:
        # Addon allows the proxy image to function as a subscription registry center and dynamically load the service route.
        aliyun.custom_addon: "proxy"
        # A proxy image container is deployed on each virtual machine (VM).
        aliyun.global: "true"
        # A Server Load Balancer instance is bound to the frontend, and the lb label is used.
        aliyun.lb.port_6379: tcp://proxy_test:6379
        # Indicates that the custom routing must be started after the master Redis and slave Redis are started, and the custom routing depends on the master Redis and slave Redis.
        aliyun.depends: redis-master,redis-slave
    environment:
        # Indicates the range of backend containers that support route loading. "*" indicates the whole cluster. By default, it indicates the services in applications.
        ADDITIONAL_SERVICES: "*"
        EXTRA_DEFAULT_SETTINGS: "log rsyslog local0,log global,option httplog"
        # Configures HAProxy to work in TCP mode.
        MODE: "tcp"
    links:
        - rsyslog:rsyslog
rsyslog:
    image: registry.cn-hangzhou.aliyuncs.com/linhuatest/rsyslog:latest
redis-master:
    ports:
      - 6379/tcp
    image: 'redis:alpine'
    labels:
        # Indicates that the custom routing is to expose the port 6379.
        aliyun.proxy.TCP_PORTS: "6379"
        # Indicates that the service route is to be added to the custom routing.
        aliyun.proxy.required: "true"
redis-slave:
    ports:
      - 6379/tcp
    image: 'redis:alpine'
    links:
      - redis-master
    labels:
      # Indicates that the custom routing is to expose the port 6379.
      aliyun.proxy.TCP_PORTS: "6379"
      # Indicates that the service route is to be added to the custom routing.
      aliyun.proxy.required: "true"
      # Indicates that the slave Redis depends on the master Redis and must be started after the master Redis is started.
      aliyun.depends: redis-master
    command: redis-server --slaveof redis-master 6379
redis-client:
    image: 'redis:alpine'
    links:
      - lb:www.example.com
    labels:
      aliyun.depends: lb
    command: redis-cli -h www.example.com
    stdin_open: true
    tty: true
```

This solution provides a master-slave Redis architecture and balances load by using the[EN-US\_TP\_7112.md\#section\_wqf\_v3z\_xdb](reseller.en-US/User Guide/Service discovery and load balancing/Custom routing - simple sample.md#section_wqf_v3z_xdb) to make Container Server become highly available.

