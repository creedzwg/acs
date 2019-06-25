# Cluster parameter configurations {#concept_g4x_rmk_xdb .concept}

This document aims to help you understand what the parameters on the page mean when you create a cluster. Then, you can configure the parameters smoothly. For some parameters, some documents are provided for your reference.

## Cluster Name {#section_xwq_smk_xdb .section}

Configure the cluster name.

-   The name can be 1–63 characters long and contain numbers, Chinese characters, English letters, and hyphens \(-\), but cannot start with a hyphen \(-\).
-   You can modify the cluster name on the Cluster List page after creating the cluster.

## Region and Zone {#section_zwq_smk_xdb .section}

## Network Type {#section_axq_smk_xdb .section}

Select VPC as the network type of the ECS instances. Alibaba Cloud Virtual Private Cloud \(VPC\) allows you to create a custom VPC. Layer-2 logical isolation exists between different VPCs. You can plan the Classless Inter-Domain Routing \(CIDR\) block of each cluster flexibly. VPC is applicable to a scenario with large-scale container clusters and provides higher security and flexibility. To better guarantee the system security and the support of hybrid cloud business, Container Service does not support creating clusters whose network type is classic network or with non-I/O optimized instance since January 1, 2018.

## Initial CIDR Block of Container Service {#section_bxq_smk_xdb .section}

Configure this parameter only when you select VPC. When planning the CIDR block, make sure the container initial CIDR block does not overlap with the VPC CIDR block.

-   You can only specify one CIDR block for each VPC. 172.16.0.0/12 is the default VPC CIDR block.
-   Specify the corresponding container CIRD block when creating a Container Service cluster. Currently, Container Service supports the following container CIDR blocks: 192.168.1.0/24 and 172.\[ 16-31\]. 1.0/24

## Add Node {#section_dxq_smk_xdb .section}

Container Service has two ways to add nodes: create nodes and add existing nodes. If you select Add, Container Service is authorized to automatically create ECS instances when the cluster is created and automatically add the created ECS instances to the created cluster. If you select Do not Add, the existing ECS instances are added to the cluster. You can add the existing ECS instances on the Create Cluster page directly or create a zero-node cluster and then add the existing ECS instances on the Cluster List page. For more information, see [Add an existing ECS instance](reseller.en-US/User Guide/Clusters/Add an existing ECS instance.md#).

## Node Type {#section_exq_smk_xdb .section}

The node type is Pay-As-You-Go by default. After creating the ECS instances, you can go to the ECS console to change the Pay-As-You-Go ECS instances to monthly or yearly subscription ECS instances.

## Operating System {#section_fxq_smk_xdb .section}

Select the operating system installed in the ECS instances. We recommend that you use Ubuntu 14.04 64 bit and CentOS 7.4 64 bit.

## Instance Generation and Instance Family {#section_gxq_smk_xdb .section}

## Instance Type {#section_hxq_smk_xdb .section}

ECS instance type defines two basic attributes: the CPU configuration and memory configuration of the instance. However, ECS instances can determine the specific service pattern of an instance only by working together with the disk, image, and network type.

## Instance Quantity {#section_ixq_smk_xdb .section}

The number of the ECS instances to be created. The number of ECS instances in one cluster cannot exceed 20. To enhance the cluster availability, we do not recommend that you create a cluster with one node. 2 sets is the default value in the console.

## System Disk Type {#section_jxq_smk_xdb .section}

## Data disk configurations {#section_kxq_smk_xdb .section}

Select the type of the data disk that is to be mounted to the container. Select the **Attach Data Disk** check box and select the data disk capacity. The data disk is mounted to the/var/lib/docker directory of the container to store the image data and container data.

## Logon Password and Confirm Password {#section_lxq_smk_xdb .section}

Enter and confirm the logon password of the ECS instances. The password is 8–30 characters long and must contain uppercase letters/lowercase letters, numbers, and special characters at the same time. This password is required when you log on to the ECS console or log on to the ECS instance by using SSH.

**Note:** 

-   Container Service uses this password to configure the ECS instances when creating the cluster, but does not save this password.
-   Keep this password properly for the initialization usage.

## EIP {#section_nxq_smk_xdb .section}

## Server Load Balancer {#section_oxq_smk_xdb .section}

An Internet Server Load Balancer instance is created by default if a cluster is created. The billing method is Pay-As-You-Go. The created Server Load Balancer instance is used to distribute the traffic to control the services and implement the service high availability.

## Monitoring Plug-in {#section_pxq_smk_xdb .section}

Select the check box to install the cloud monitoring plug-in on the ECS instances. Then, the operating system-level performance indicators of the ECS instances in the cluster can be monitored.

## RDS Whitelist {#section_qxq_smk_xdb .section}

You can select to add the IP addresses of the created nodes to the RDS instance whitelist, which facilitates the ECS instances to access the RDS instances.

-   We recommend that you configure the RDS Whitelist when **Add** is selected for Add Node.
-   If **Do not Add** is selected for Add Node and you want to configure the RDS Whitelist, add the existing ECS instances on the Create Cluster page. The RDS Whitelist cannot be configured if you create a zero-node cluster and add existing ECS instances after the cluster creation.
-   The ECS instance must be in the same region as the RDS instance so that the IP address of the ECS instance can be added to the RDS instance whitelist.

## Security Group {#section_sxq_smk_xdb .section}

Container Service configures the default security group and only sets the inbound security group rules. You can configure the security group according to your business scenarios after the cluster is created successfully. For more information, see [Container Service security group rules](reseller.en-US/User Guide/Security groups/Container Service security group rules.md#)

-   Ports 443 and 80 can be opened or closed as per your needs.
-   We recommend that you retain the ICMP rules for communication between nodes and the convenience of troubleshooting. Some tools also depend on ICMP.
-   Make sure you open all the ports you need. Otherwise, some services become inaccessible. The port that is accessed by using Server Load Balancer is not required to be opened.

