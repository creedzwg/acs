# Environment preparations {#concept_svl_yt5_vdb .concept}

Before using the Alibaba Cloud Container Service blockchain solution, complete the corresponding environment preparations, which mainly includes:

-   Create a Kubernetes cluster
-   Bind an Elastic IP \(EIP\) to a worker node
-   Create a file system and add a mount point

This document introduces how to prepare the environment.

## Create a Kubernetes cluster {#section_lh2_355_vdb .section}

The blockchain solution deployment is based on a Kubernetes cluster built by ECS. For how to create a Kubernetes cluster, see [Create a Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#) . When creating a Kubernetes cluster, you must complete the following key configurations to make sure the blockchain solution can be deployed successfully.

-   Region: The Kubernetes cluster and the NAS file system must be in the same region. Make sure the selected region supports both the Kubernetes cluster and the NAS file system \(intersection of both\). For the list of regions that support the NAS file system, see **Products** \> **Storage & CDN** \> **NAS** \> **File System List**.
-   Network type: Select Virtual Private Cloud \(VPC\).
-   SSH login: For ease of management, select the **Enable SSH access for Internet** check box.
-   Node configurations: We recommend that you use the default settings \(such as three master nodes and three worker nodes\). You can also configure the nodes as per your needs. The numbers of softwares, services, and containers deployed by the blockchain network are large. Therefore, make sure the cluster resources can meet the requirements.

Click Create Cluster. Wait several minutes \(depends on the number of ECS instances\), until the Kubernetes cluster and the ECS instances are created.

After the Kubernetes cluster is created, click **Manage** at the right of the created cluster on the Cluster List page. On the **Basic Information page** \> **Connection Information** section, record the Master node SSH IP address, which is an Internet address and used as the external address.

Click **Kubernetes** \> **Nodes**in the left-side navigation pane. Record the IP addresses \(intranet addresses\) of the cluster nodes \(both master nodes and worker nodes\) for later usage.

## Bind an EIP to a worker node {#section_ph2_355_vdb .section}

This section introduces the prerequisite for the access to the blockchain network from outside the cluster, which creates and binds an EIP to a worker node of the Kubernetes cluster.

**Procedure**

1.  Log on to the [Elastic IP Address \(EIP\) console](https://partners-intl.console.aliyun.com/#/ip).
2.  Click **Elastic IP Addresses** in the left-side navigation pane.
3.  Click **Create EIP**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7393/15613700332641_en-US.png)

4.  Select the region in which the Kubernetes cluster resides. Complete the other configurations as per your needs. Click **Buy Now**.
5.  After activating the EIP, wait until the purchased EIP is displayed in the list and then click **Bind** at the right of the EIP.
6.  Select ECS Instance from the Instance Type drop-down list and select a worker node \(the instance name begins with node, instead of master\) from the ECS Instance drop-down list. Click **OK**.
7.  After binding the EIP to the worker node, record the IP address of the purchased EIP on the Elastic IP Addresses page.

## Create a file system and add a mount point {#section_vh2_355_vdb .section}

In the blockchain solution, a file system is mainly used to store and distribute the certificates, keys, and configurations of the blockchain and persistently store the data of the blockchain master nodes.

**Note:** The usage of a file system only applies to the development and test phases of blockchain applications and solutions. For deployment in the production environment, contact us to further discuss specific business and technical needs and determine the most suitable method.

For how to create a file system, see [../../SP\_111/DNnas1858274/EN-US\_TP\_18690.dita\#concept\_27526\_zh](../../SP_111/DNnas1858274/EN-US_TP_18690.dita#concept_27526_zh) . When creating a file system, you must complete the following key configurations to make sure the blockchain solution can be deployed successfully.

-   Region: Select the region in which the container cluster resides.
-   Protocol Type: Select NFS.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7393/15613700342642_en-US.png)


For more information, see [../../SP\_111/DNnas1858274/EN-US\_TP\_18691.dita\#concept\_60431\_zh](../../SP_111/DNnas1858274/EN-US_TP_18691.dita#concept_60431_zh).

After adding a mount point, click **Manage** at the right of the file system.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7393/15613700342644_en-US.png)

Record the Mount Address of the mount point.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7393/15613700342646_en-US.png)

