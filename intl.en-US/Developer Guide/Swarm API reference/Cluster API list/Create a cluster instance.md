# Create a cluster instance {#reference_yy4_rcd_xdb .reference}

Create a cluster with a specified number of new nodes.

## Request information {#section_tqj_tcd_xdb .section}

**Request line \(RequestLine\)**

``` {#codeblock_1py_s7y_nmj}
POST /clusters HTTP/1.1
```

**Request line parameter \(URI Param\)**

None.

**Special request header \(RequestHead\)**

None. See [Public request headers](reseller.en-US/Developer Guide/Swarm API reference/Cluster API call mode/Public parameters.md#section_cdv_4nd_xdb).

**Request body \(RequestBody\)**

``` {#codeblock_hw5_67t_ed7}
{
    "password": "password of the root account to log on to the Elastic Compute Service (ECS) instance",
    "region_id": "RegionID",
    "instance_type": "ECS instance type",
    "name": "cluster name",
    "size": "number of nodes",
    "network_mode": "vpc",
    "vpc_id": "VPC_ID",
    "vswitch_id": "VSwitch ID",
    "subnet_cidr": "container Classless Inter-Domain Routing (CIDR) block such as 172.28.1.0/24",
     "data_disk_category": "disk category",
    "data_disk_size": "disk size",
    "need_slb": "whether to create Server Load Balancer instance for the cluster",
    "ecs_image_id": "operating system image",
    "io_optimized": "whether I/O to be optimized",
    "release_eip_flag": "whether to release Elastic IP (EIP) after configuring the cluster"
    "rds_instances": "RDS instance ID"
}
```

**Request body explanation**

|Name|Type|Required|Description|
|:---|:---|:-------|:----------|
|name|string|Yes|The cluster name, which can contain uppercase English letters, lowercase English letters, Chinese characters, numbers, and hyphens \(-\).|
|size|integer|Yes|The number of ECS instances in the cluster.|
|instance\_type|string|Yes|The ECS instance type code. For more information, see [Instance type families](../../../../reseller.en-US/Instances/Instance type families.md#).|
|network\_mode|string|Yes|The network mode of the cluster. Currently, only VPC is supported.|
|subnet\_cidr|string|Yes|The CIDR block that can be used by the cluster, for example, 192.168.24.0/22. This field must be configured only when the network mode is VPC.|
|vpc\_id|string|Yes| The VPC ID. This field must be configured only when the network mode is VPC. For more information, see [../../SP\_22/DNVPC11839992/EN-US\_TP\_80559.dita\#concept\_sgp\_twv\_dgb](../../SP_22/DNVPC11839992/EN-US_TP_80559.dita#concept_sgp_twv_dgb).

 |
|vswitch\_id|string|Yes.|The VSwitch ID of the VPC. This field must be configured only when the network mode is VPC.|
|password|string|Yes|The password of the root account.|
|data\_disk\_category|string|Yes| The disk category used by ECS. For more information, see [../../SP\_2/DNA0011860945/EN-US\_TP\_10045.dita\#EcsApiDiskCategroy](../../SP_2/DNA0011860945/EN-US_TP_10045.dita#EcsApiDiskCategroy).

 |
|data\_disk\_size|number|Yes|The disk size shared by nodes.|
|ecs\_image\_id|string|No|The ID of the system image used by ECS. For more information, see [View image list](reseller.en-US/Developer Guide/Swarm API reference/Cluster API list/View image list.md#).|
|io\_optimized|string|No|Determined according to the ECS instance rule. Optional values: none or optimized. We recommend that you pass in optimized because currently only VPC is supported as the network mode.|
|need\_slb|bool|No|Whether to create the default simple routing Server Load Balancer instance for the cluster. The default value is true.|
|release\_eip\_flag|bool|No|Whether or not to release EIP after configuring the cluster. The default value is false.|
|rds\_instances|array|No|Whether to add the IP address of the ECS instance to the RDS instance whitelist.|

**ecs\_image\_id list**

See [View image list](reseller.en-US/Developer Guide/Swarm API reference/Cluster API list/View image list.md#) to obtain the ecs\_image\_id list. To customize the ECS image ID of the cluster, make sure the ECS image meets the following requirements:

-   Operating system: Ubuntu or CentOS.
-   The Linux Kernel is 3.18 version or later, which is used to support overlayfs and overlay network.
-   The /etc/docker/key.json file is deleted from the image.

## Response information {#section_yzv_wdd_xdb .section}

**Response line \(ResponseLine\)**

``` {#codeblock_6wf_jdo_lsc}
HTTP/1.1 202 Accepted
```

**Special response header \(ResponseHead\)**

None. See [Public response headers](reseller.en-US/Developer Guide/Swarm API reference/Cluster API call mode/Public parameters.md#section_qdv_4nd_xdb).

**Response body \(ResponseBody\)**

``` {#codeblock_kxi_qfo_z4j}
{
    "cluster_id":"string",
    "request_id":"string",
    "task_id":"string"
}
```

## Example {#section_fgy_zdd_xdb .section}

**Request example**

``` {#codeblock_yv1_6ua_yg6}
POST /clusters HTTP/1.1
<Public request headers>
{
    "password": "TestPwd124",
    "region_id": "cn-beijing",
    "instance_type": "ecs.n1.small",
    "name": "my-test-cluster",
    "size": 1,
    "network_mode": "vpc",
    "vpc_id":"vpc-xxxx",
    "vswitch_id":"vsw-xxxx",
    "subnet_cidr":"172.28.1.0/24",
    "data_disk_category": "cloud_ssd",
    "data_disk_size": 40,
    "need_slb":true,
    "ecs_image_id":"centos_7_04_64_20G_alibase_201701015",
    "io_optimized":"true",
    "release_eip_flag":false
}
```

**Response example**

``` {#codeblock_ccp_qb9_ohs}
HTTP/1.1 202 Accepted
<Public response headers>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650d7ce",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

