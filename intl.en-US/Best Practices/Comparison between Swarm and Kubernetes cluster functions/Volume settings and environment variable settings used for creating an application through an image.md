# Volume settings and environment variable settings used for creating an application through an image {#concept_kwp_c1t_ggb .concept}

This topic compares the volume settings and the environment variable settings used in a Swarm cluster with those used in a Kubernetes cluster for creating an application through an image.

## Create an application by using an image {#section_o3z_bsl_ggb .section}

If you create an application in the Container Service console by using an image, the Swarm cluster Web interface is different from the Kubernetes cluster Web interface.

-   For more information about the Web interface of a Swarm cluster, see [Create an application ](../../../../../reseller.en-US/User Guide/Applications/Create an application .md#).
-   For more information about the Web interface of a Kubernetes cluster, see [Create a deployment application by using an image](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#).

## Set a volume {#section_ojm_sbt_ggb .section}

**Container Service Swarm clusters**

Specify your cloud or local storage path.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83740/154829382335444_en-US.png)

**Container Service Kubernetes clusters**

In Container Service, storage devices can be used in the same way in both Kubernetes and Swarm clusters, which have basically the same cluster console interface settings. However, the storage devices are mounted with different methods in these two types of clusters.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83740/154829382435447_en-US.png)

You can use either a local storage device or a cloud storage device.

-   Available local storage types include HostPath, ConfigMap, Secret, and EmptyDir.
-   Available cloud storage types include cloud disk, NAS, and OSS.

## Set environment variables {#section_y5z_pft_ggb .section}

The Environment parameter can be set with the same method for Swarm clusters and Kubernetes clusters. You only need to specify keys and their corresponding values.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83740/154829382435452_en-US.png)

