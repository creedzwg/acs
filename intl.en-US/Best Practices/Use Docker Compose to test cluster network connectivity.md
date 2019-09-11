# Use Docker Compose to test cluster network connectivity {#concept_nwd_q41_ydb .concept}

This document provides a simple Compose file used to realize one-click deployment and you can test the container network connectivity by visiting the service access endpoint.

## Scenarios {#section_ojw_y41_ydb .section}

When deploying interdependent applications in a Docker cluster, you must make sure that the applications can access each other to realize cross-host container network connectivity. However, sometimes containers on different hosts cannot access each other due to network problems. If this happens, it is difficult to troubleshoot the problem. Therefore, an easy-to-use Compose file can be used to test the connectivity among cross-host containers within a cluster.

## Solutions {#section_pjw_y41_ydb .section}

Use the provided image and orchestration template to test the connectivity among containers.

``` {#codeblock_meu_r3f_86g}
web:
  image: registry.aliyuncs.com/xianlu/test-link
  command: python test-link.py
  restart: always
  ports:
      - 5000
  links:
      - redis
  labels:
      aliyun.scale: '3'
      aliyun.routing.port_5000: test-link;
redis:
  image: redis
  restart: always
```

This example uses Flask to test the container connectivity.

The preceding orchestration template deploys a Web service and a Redis service. The Web service contains three Flask containers and these three containers will be evenly distributed to three nodes when started. The three containers are on different hosts and the current network can realize cross-host container connectivity if the containers can ping each other. The Redis service runs on one of the three nodes. When started, each Flask container registers to the Redis service and reports the container IP address. The Redis service has the IP addresses of all the containers in the cluster after the three Flask containers are all started. When you access any of the three Flask containers, the container will send ping command to the other two containers and you can check the network connectivity of the cluster according to the ping command response.

## Procedure {#section_kdh_3y2_zdb .section}

1.  Create a cluster which contains three nodes.

    In this example, the cluster name is **test-link**. For how to create a cluster, see [Create a cluster](../../../../reseller.en-US/User Guide/Clusters/Create a cluster.md#).

    **Note:** Select to create a Server Load Balancer instance when creating the cluster.

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/47621/intl_zh/1516675830403/Image%2012.png)

2.  Use the preceding template to create an application \(in this example, the application name is **test-cluster-link**\) to deploy the **web** service and **redis** service.

    For how to create an application, see[Create an application](../../../../reseller.en-US/User Guide/Applications/Create an application.md#).

3.  On the Application List page, click the application name to view the created services.

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/47621/intl_zh/1516675935978/Image%2013.png)

4.  Click the name of the **web** service to enter the service details page.

    You can see that the three containers \(**test-cluster-link\_web\_1**, **test-cluster-link\_web\_2**, and **test-cluster-link\_web\_3**\) are all started and distributed on different nodes.

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/47621/intl_zh/1516676387423/Image%2014.png)

5.  Visit the access endpoint of the **web** service.

    As shown in the following figure, the container **test-cluster-link\_web\_1** can access the container **test-cluster-link\_web\_2** and container **test-cluster-link\_web\_3**.

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/47621/cn_zh/1482482623182/%E7%BD%91%E7%BB%9C3.png)

    Refresh the page. As shown in the following figure, the container **test-cluster-link\_web\_2** can access the container **test-cluster-link\_web\_1** and container **test-cluster-link\_web\_3**.

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/47621/cn_zh/1482738388718/%E7%BD%91%E7%BB%9C5.png)

    As the preceding results show, the containers in the cluster can access each other.


