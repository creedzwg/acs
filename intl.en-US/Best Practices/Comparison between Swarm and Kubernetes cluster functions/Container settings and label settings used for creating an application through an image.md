# Container settings and label settings used for creating an application through an image {#concept_mf4_dgt_ggb .concept}

This topic compares the container and label settings used in a Swarm cluster with those used in a Kubernetes cluster for creating an application through an image.

## Create an application by using an image {#section_o3z_bsl_ggb .section}

When you create an application in the Container Service console by using an image, you will see that the Web interfaces are different in a Swarm cluster and a Kubernetes cluster.

-   For more information about the Web interface of a Swarm cluster, see [Create an application ](../../../../../reseller.en-US/User Guide/Applications/Create an application .md#).
-   For more information about the Web interface of a Kubernetes cluster, see [Create a deployment application by using an image](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#).

## Container settings {#section_grt_ggt_ggb .section}

**Container Service Swarm clusters**

You can set container startup commands \(through the **Command** parameter and the **Entrypoint** parameter\), resource limits \(including **CPU Limit** and **Memory Limit**\), Container Config, and other parameters.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83757/154829385235453_en-US.png)

**Container Service Kubernetes clusters**

The Container settings of the Swarm cluster are similar to the life cycle settings and some general settings of the Kubernetes cluster.

-   Life Cycle settings include the following parameters. For more information about the parameter description, see [Create a deployment application by using an image](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#).

    -   **Start**
    -   **Post Start**
    -   **Pre Stop**
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83757/154829385235526_en-US.png)

-   General settings include the following parameters. For more information about the parameter description, see [Create a deployment application by using an image](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#). For more information about setting parameters, see [Recommended Kubernetes cluster configurations to run highly reliable applications](../../../../../reseller.en-US/Best Practices/Cluster/ECS instance selection and cluster configurations/Recommended Kubernetes cluster configurations to run highly reliable applications.md#).
    -   **Resource Limit**
    -   **Resource Request**

## Label {#section_tt4_srz_ggb .section}

**Container Service Swarm clusters**

With labels, you can set health checks, access domain names, logs, and other functions.

**Container Service Kubernetes clusters**

A label can only mark an application in a Kubernetes cluster. Different methods are used in a Kubernetes cluster to implement the functions that are implemented through labels in a Swarm cluster, such as health checks and access domain names.

When you create an application in a Kubernetes cluster by using an image, a label of the same name as the application is created. The label is not displayed on the application configuration page. You can use labels in YAML files.

