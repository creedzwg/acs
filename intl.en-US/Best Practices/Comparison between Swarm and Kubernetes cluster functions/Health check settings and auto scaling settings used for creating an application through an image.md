# Health check settings and auto scaling settings used for creating an application through an image {#concept_kg5_rsz_ggb .concept}

This topic compares the health check settings and the auto scaling settings used in a Swarm cluster and those used in a Kubernetes cluster for creating an application through an image.

## Create an application by using an image {#section_o3z_bsl_ggb .section}

When you create an application in the Container Service console by using an image, you will see that the Web interfaces are different in a Swarm cluster and a Kubernetes cluster.

-   For more information about the Web interface of a Swarm cluster, see [Create an application ](../../../../../reseller.en-US/User Guide/Applications/Create an application .md#).
-   For more information about the Web interface of a Kubernetes cluster, see [Create a deployment application by using an image](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#).

## Set health checks {#section_dfr_5sz_ggb .section}

**Container Service Swarm clusters**

Health checks are implemented through labels.

**Container Service Kubernetes clusters**

If you use an image to create an application, you can set health checks on the Container tab page. You can set a **Liveness** probe and a **Readiness** probe.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/84654/154829387635542_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/84654/154829387635543_en-US.png)

## Set auto scaling {#section_krz_vsz_ggb .section}

**Container Service Swarm clusters**

You can set auto scaling according to CPU usage and memory usage.

**Container Service Kubernetes clusters**

You can set auto scaling according to CPU usage and memory usage by enabling Horizontal Pod Autoscaling \(HPA\).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/84654/154829387635544_en-US.png)

