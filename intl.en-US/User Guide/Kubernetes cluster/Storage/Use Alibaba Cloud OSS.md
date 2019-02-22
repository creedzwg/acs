# Use Alibaba Cloud OSS {#concept_h11_5bt_vdb .concept}

You can use the Alibaba Cloud Object Storage Service \(OSS\) data volumes in Alibaba Cloud Container Service Kubernetes clusters.

Currently, OSS static storage volumes are supported, while OSS dynamic storage volumes are not supported. You can use the OSS static storage volumes by:

-   Using the volume method.
-   Using PV/PVC.

## Prerequisites {#section_dyz_ybt_vdb .section}

You must create a bucket in the OSS console before using the OSS static storage volumes.

## Instructions {#section_dpz_zbt_vdb .section}

-   OSS is a shared storage and can provide shared storage service for multiple pods at the same time.
-   bucket: Currently, Container Service only supports mounting buckets and cannot mount the sub-directories or files under the bucket.
-   url: The OSS endpoint, which is the access domain name for mounting OSS.
-   akId: Your AccessKey ID.
-     akSecret: Your AccessKey Secret.
-   otherOpts: Customized parameter input in the format of `-o *** -o ***` is supported when mounting OSS.

## Note {#section_gyz_ybt_vdb .section}

If your Kubernetes cluster is created before Feb 6th, 2018, [Install the plug-in](reseller.en-US/User Guide/Kubernetes cluster/Storage/Install the plug-in.md#) before using the data volumes. To use OSS data volumes, you must create the secret and enter the AccessKey information when deploying the flexvolume service.

## Use OSS static storage volumes {#section_d13_bct_vdb .section}

**Use volume method**

Use the oss-deploy.yaml file to create the pod.

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-oss-deploy
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-flexvolume-oss
        image: nginx
        volumeMounts:
          - name: "oss1"
            mountPath: "/data"
      volumes:
        - name: "oss1"
          flexVolume:
            driver: "alicloud/oss"
            options:
              bucket: "docker"
              url: "oss-cn-hangzhou.aliyuncs.com"
              akId: ***
              akSecret: ***
              otherOpts: "-o max_stat_cache_size=0 -o allow_other"
```

**Use PV/PVC \(currently, dynamic pv is not supported\)**

**Step 1 Create PV**

You can create the PV in the Container Service console or by using the YAML file.

**Create PV by using YAML file**

Use the oss-pv.yaml file to create the PV.

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-oss
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  storageClassName: oss
  flexVolume:
    driver: "alicloud/oss"
    options:
      bucket: "docker"
      url: "oss-cn-hangzhou.aliyuncs.com"
      akId: ***
      akSecret: ***
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
```

**Create OSS data volumes in Container Service console**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, choose **Clusters** \> **Storage**.
3.  Select the cluster from the Clusters drop-down list and then click **Create** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6938/15508197254683_en-US.png)

4.  The Create Data Volume dialog box appears. Configure the data volume parameters.

    -   **Type**: Select OSS in this example.
    -   **Name**: Enter the name of the data volume you are about to create. The data volume name must be unique in the cluster. In this example, enter pv-oss.
    -   **Capacity**: Enter the capacity of the data volume to be created.
    -   **Access Mode**: ReadWriteMany by default.
    -   **Access Key ID**/**Access Key Secret**: The AccessKey required to access OSS.
    -   **Bucket ID**: Select the OSS bucket name you want to use. Click **Select Bucket**. Select the bucket in the displayed dialog box and click **Select**.
    -   **Access Domain Name**: If the bucket and Elastic Compute Service \(ECS\) instance are in different regions, select **Internet**. If the bucket and ECS instance are in the same region, select Intranet or VPC according to the cluster network type. Select **VPC** if the network type is Virtual Private Cloud \(VPC\) or select **Intranet** if the network type is classic network.
    -   **Tag**: Click Add Tag to add tags for this data volume.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6938/15508197254684_en-US.png)

5.  Click **Create** after completing the configurations.

**Step 2 Create PVC**

Use the oss-pvc.yaml file to create the PVC.

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-oss
spec:
  storageClassName: oss
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
```

**Step 3 Create pod**

Use the oss-pod.yaml file to create the pod.

```
apiVersion: v1
kind: Pod
metadata:
  name: "flexvolume-oss-example"
spec:
  containers:
    - name: "nginx"
      image: "nginx"
      volumeMounts:
          - name: pvc-oss
            mountPath: "/data"
  volumes:
  - name: pvc-oss
    persistentVolumeClaim:
        claimName: pvc-oss
```

## Use OSS dynamic storage volumes {#section_ozz_ybt_vdb .section}

Currently not supported.

