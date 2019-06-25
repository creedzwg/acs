# Expand a cluster {#reference_bjp_sgd_xdb .reference}

Increase the number of nodes in the cluster.

## Request information {#section_i3y_tgd_xdb .section}

 **Request line \(RequestLine\)** 

``` {#codeblock_hl8_eun_vn8}
PUT /clusters/{cluster_id} HTTP/1.1
```

 **Request line parameter \(URI Param\)** 

|Name|Type|Required|Description|
|----|----|--------|-----------|
| cluster\_id |string|Yes|Cluster ID|

 **Special request header \(RequestHead\)** 

None. See [Public request headers](intl.en-US/Developer Guide/Swarm API reference/Cluster API call mode/Public parameters.md#section_cdv_4nd_xdb).

 **Request body \(RequestBody\)** 

``` {#codeblock_e8g_reh_eby}
{
    "password": "password of the root account to log on to the Elastic Compute Service (ECS) instance",
    "instance_type": "ECS instance type",
     "size": "number of nodes after expansion",
     "data_disk_category": "disk category",
    "data_disk_size": "disk size",
    "ecs_image_id": "operating system image",
    "io_optimized": "whether I/O to be optimized",
    "release_eip_flag": "whether to release Elastic IP (EIP) after configuring the cluster"
}
```

 **Request body explanation** 

|Name|Type|Required|Description|
|:---|:---|:-------|:----------|
| password |string|Yes|The password of the ECS instance.|
| instance\_type |string|Yes|Code indicating the ECS instance type. For more information, see [Instance type families](../../../../intl.en-US/Instances/Instance type families.md#).|
| size |integer|Yes|The total number of nodes, which must be larger than the number of the existing nodes.|
| data\_disk\_category |string|No| The disk category used by ECS. For more information, see [../../SP\_2/DNA0011860945/EN-US\_TP\_10045.dita\#EcsApiDiskCategroy](../../SP_2/DNA0011860945/EN-US_TP_10045.dita#EcsApiDiskCategroy).

 |
| data\_disk\_size |number|No|The disk size shared by nodes \(unit: GB\).|
| ecs\_image\_id |string|Yes|The ID of the system image used by ECS.|
| io\_optimized |string|No|Determined according to the ECS instance rule. Optional values: none or optimized.|
| release\_eip\_flag |bool|No|Whether to release EIP after configuring the cluster. The default value is false.|

 **ecs\_image\_id list** 

See [View image list](intl.en-US/Developer Guide/Swarm API reference/Cluster API list/View image list.md#) to obtain the ecs\_image\_id list. To customize the ECS image ID of the cluster, make sure the ECS image meets the following requirements:

-   Operating system: Ubuntu or CentOS.
-   The Linux Kernel is 3.18 version or later, which is used to support overlayfs and overlay network.
-   The /etc/docker/key.json file is deleted from the image.

## Response information {#section_jcq_khd_xdb .section}

 **Response line \(ResponseLine\)** 

``` {#codeblock_yui_v4n_tel}
HTTP/1.1 200 OK
```

 **Special response header \(ResponseHead\)** 

None. See [Public response headers](intl.en-US/Developer Guide/Swarm API reference/Cluster API call mode/Public parameters.md#section_qdv_4nd_xdb).

 **Response body \(ResponseBody\)** 

``` {#codeblock_fc9_zzl_8sa}
{
    "cluster_id":"string",
    "request_id":"string",
    "task_id":"string"
}
```

## Example {#section_jwq_nhd_xdb .section}

 **Request example** 

``` {#codeblock_sra_ri4_gqp}
PUT /clusters/Cccfd68c474454665ace07efce924f75f HTTP/1.1
<Public request headers>
{
  "password": "password",
  "instance_type": "ecs.s3.large",
  "size": 2,
  "data_disk_category": "cloud_ssd",
  "data_disk_size": 500,
  "ecs_image_id": "centos_7_2_64_40G_base_20170222.vhd",
  "io_optimized":"optimized",
  "release_eip_flag": false,
}
```

 **Response example** 

``` {#codeblock_lc7_9ok_dft}
HTTP/1.1 202 Accepted
<Public response headers>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650d7ce",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

