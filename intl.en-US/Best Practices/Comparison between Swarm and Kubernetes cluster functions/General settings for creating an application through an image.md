# General settings for creating an application through an image {#concept_dcx_j1t_ggb .concept}

This topic compares the general settings used in a Swarm cluster and those used in a Kubernetes cluster for creating an application through an image.

## Create an application by using an image {#section_csb_z1t_ggb .section}

If you create an application in the Container Service console by using an image, the Swarm cluster Web interface is different from the Kubernetes cluster Web interface.

-   For more information about the Web interface of a Swarm cluster, see [Create an application ](../../../../../reseller.en-US/User Guide/Applications/Create an application .md#).
-   For more information about the Web interface of a Kubernetes cluster, see [Create a deployment application by using an image](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#).

## Basic information {#section_z5x_w1t_ggb .section}

**Container Service Swarm clusters**

The basic information for creating an application in a Swarm cluster includes the application name, application version, deployment cluster, default update policy, and application description.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829455035310_en-US.png)

**Container Service Kubernetes clusters**

The basic information for creating an application in a Kubernetes cluster includes the application name, application version, deployment cluster, namespace, number of replicas, and application type.

The namespace term is exclusive to Kubernetes clusters. Kubernetes uses namespaces to isolate resources such as CPU and memory. In addition, namespaces can be used to separate different environments such as test and development environments. We recommend that you use clusters to isolate production environments. For information about the namespace term, see [Terms](../../../../../reseller.en-US/Product Introduction/Terms.md#).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829455035309_en-US.png)

## General settings {#section_o3z_bsl_ggb .section}

The image name and image version settings are the most important.

**Container Service Swarm clusters**

The **Network Mode** supports**Default** and **host**.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829455035331_en-US.png)

**Container Service Kubernetes clusters**

-   The network mode of the application has been specified when you create the cluster. Available network plugins include **Flannel** and **Terway**. For more information, see [Do I select the Terway or Flannel plug-in for my Kubernetes cluster network?](../../../../../reseller.en-US/FAQ/Do I select the Terway or Flannel plug-in for my Kubernetes cluster network?.md#).
-   Required resources include the CPU and memory resources required by the application. The resource limits are the upper thresholds of the resources quota. You can compare the settings with the **CPU Limit** and **Memory Limit** settings of the Container settings in a Swarm cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829455035332_en-US.png)

