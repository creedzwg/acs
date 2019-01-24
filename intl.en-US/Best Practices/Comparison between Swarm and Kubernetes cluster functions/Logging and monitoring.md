# Logging and monitoring {#concept_ssv_jh1_hgb .concept}

This topic compares logging and monitoring functions of a Swarm cluster with those of a Kubernetes cluster.

## Logging {#section_tyw_wtn_jgb .section}

**Swarm cluster**

For a Swarm cluster, the logging function is implemented through **labels**.

**Kubernetes cluster**

For a Kubernetes cluster, the logging function is configured and used in the following scenarios:

-   Create a Kubernetes cluster.

    On the Create Kubernetes Cluster page, select the **Using Log Service** check box. Then the Log Service plugin is automatically installed in the cluster. You can use an existing project or create a new project.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/84981/154829398236258_en-US.png)

    You can also manually install Log Service components in the created cluster. For more information, see [Manually install Log Service components in a created Kubernetes cluster](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Log management/Use Log Service to collect Kubernetes cluster logs.md#section_shf_y5r_gfb).

-   Configure Log Service when creating an application. For more information, see [Configure Log Service when creating an application](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Log management/Use Log Service to collect Kubernetes cluster logs.md#section_g3f_y5r_gfb).
-   Use Log Service after creating an application. For more information, see [Container text logs](../../../../../reseller.en-US/User Guide/Logtail collection/Container log collection/Container text logs.md#) and [Containers-standard output](../../../../../reseller.en-US/User Guide/Logtail collection/Container log collection/Containers-standard output.md#).

## Monitoring {#section_ykl_c5n_jgb .section}

For both Swarm and Kubernetes clusters, select the **Install cloud monitoring plug-in on your ECS** check box on the Create Cluster page. You can then monitor the ECS instances through the CloudMonitor console.

**Swarm cluster**

By default, the monitoring function is disabled.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/84981/154829398236276_en-US.png)

**Kubernetes cluster**

By default, the monitoring function is enabled.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/84981/154829398236277_en-US.png)

For more information, see [Integration and usage with CloudMonitor](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Monitoring management/Integration and usage with CloudMonitor.md#).

