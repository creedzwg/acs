# Network settings used for creating an application through an image {#concept_ud5_wwj_ggb .concept}

This topic compares the network settings used in a Swarm cluster with those used in a Kubernetes cluster for creating an application through an image.

## Create an application by using an image {#section_o3z_bsl_ggb .section}

If you create an application in the Container Service console by using an image, the Swarm cluster Web interface is different from the Kubernetes cluster Web interface.

-   For more information about the Web interface of a Swarm cluster, see [Create an application ](../../../../../reseller.en-US/User Guide/Applications/Create an application .md#).
-   For more information about the Web interface of a Kubernetes cluster, see [Create a deployment application by using an image](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#).

**Network configuration**

The Network Configuration of a Swarm cluster is used to expose the access methods outside the cluster for an application.

## Configure port mapping {#section_fbl_gbt_ggb .section}

**Container Service Swarm clusters**

With the **Port Mapping** function of a Swarm cluster, you can map the application port to a host so that each host actives the same port. Then the application can be accessed through `<HostIP>:<Port>`.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829378335343_en-US.png)

**Container Service Kubernetes clusters**

To implement the port mapping function in a Kubernetes cluster, you can create a **NodePort** type service by using either of the following two methods:

**Method 1: Configure port mapping when creating an application**

1.  After you complete the Container setting, configure the Advanced setting. Specifically, click **Create** on the right of **Service** in the Access Control area.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829378335379_en-US.png)

2.  Select the **NodePort** **Type**. For more information, see [Create a deployment application by using an image](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829378335381_en-US.png)


**Method 2: Configure port mapping when creating a service**

1.  In the left-side navigation pane in the Container Service console, choose **Discovery and Load Balancing** \> **Service**.
2.  Select the target cluster and namespace, and click **Create**. In the Create Service dialog box, select the **NodePort** **Type**. For more information, see [Create a service](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a service.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829378335387_en-US.png)


## Configure simple routing {#section_zjf_jbt_ggb .section}

**Container Service Swarm clusters**

With the **Simple Routing** function of a Swarm cluster, you can access an application through a domain name. You can use the domain name provided by Container Service or customize the domain name.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829378335393_en-US.png)

**Container Service Kubernetes clusters**

In a Kubernetes cluster, you can create an Ingress to implement simple routing. In addition, the Ingress function of Container Service for Kubernetes provides blue/green deployment and gray releases. For more information, see [Gray releases and blue/green deployment](../../../../../reseller.en-US/Best Practices/Release/Implement a gray release and a blue__green deployment through Ingress in a Kubernetes cluster/Gray releases and blue__green deployment.md#).

Two methods are available to implement the Ingress function in a Kubernetes cluster.

**Method 1: Configure an Ingress when creating an application**

1.  After you complete the Container setting, configure the Advanced setting. Specifically, click **Create** on the right of **Ingress** in the Access Control area.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829378335395_en-US.png)

2.  For more information, see [Create a deployment application by using an image](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829378335397_en-US.png)


**Method 2: Configure an Ingress directly**

1.  In the left-side navigation pane in the Container Service console, choose **Discovery and Load Balancing** \> **Ingress**.
2.  Select the target cluster and namespace, and click **Create**. For more information, see [Create an Ingress in Container Service console](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer and Ingress management/Create an Ingress in Container Service console.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829378335397_en-US.png)


## Configure Server Load Balancer {#section_wwh_nbt_ggb .section}

**Container Service Swarm clusters**

With the **Load Balancer** function of a Swarm cluster, you can use Alibaba Cloud Server Load Balancer to expose the access method of an application. You must create an SLB and then associate the ID and the port number of the created SLB with the application so that you can access the application through <SLB\_IP\>:<Port\>.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829378335439_en-US.png)

**Container Service Kubernetes clusters**

In a Kubernetes cluster, you can also expose the access method of an application by associating an SLB with the application. An SLB can be automatically created in a Kubernetes cluster through an SLB service. For SLB access, you can select either Internet access method or internal cluster access method. If you use a YAML file to create an application, you can specify an existing SLB and set session persistence. For more information, see [Create a service](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a service.md#).

Two methods are available to create an SLB service in a Kubernetes cluster.

**Method 1: Configure an SLB service when creating an application**

1.  After you complete the Container setting, configure the Advanced setting. Specifically, click **Create** on the right of **Service** in the Access Control area.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829378335379_en-US.png)

2.  Select the **Server Load Balancer** **Type**. For more information, see [Create a deployment application by using an image](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829378435440_en-US.png)


**Method 2: Create an SLB service directly**

1.  In the left-side navigation pane in the Container Service console, choose**Discovery and Load Balancing** \> **Service**.
2.  Select the target cluster and namespace, and click **Create**. In the Create Service dialog box, select the **Server Load Balancer** **Type**. For more information, see [Create a service](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a service.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83224/154829378435441_en-US.png)


