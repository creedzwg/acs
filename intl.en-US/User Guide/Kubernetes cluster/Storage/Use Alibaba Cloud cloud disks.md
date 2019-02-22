# Use Alibaba Cloud cloud disks {#concept_l5p_qvs_vdb .concept}

You can use the Alibaba Cloud cloud disk storage volumes in Alibaba Cloud Container Service Kubernetes clusters.

Currently, Alibaba Cloud cloud disk provides the following two Kubernetes mount methods:

-   [Static storage volumes](#section_kt5_yvs_vdb)

    You can use the cloud disk static storage volumes by:

    -   [Using the volume method](#ul_jt5_yvs_vdb)
    -   [Using PV/PVC](#ul_jt5_yvs_vdb)
-   [Dynamic storage volumes](#section_t55_yvs_vdb)

**Note:** The following requirements are imposed on the created cloud disk capacity:

-   Basic cloud disk: Minimum 5Gi
-   Ultra cloud disk: Minimum 20Gi
-   SSD cloud disk: Minimum 20Gi

## Static storage volumes {#section_kt5_yvs_vdb .section}

You can use Alibaba Cloud cloud disk storage volumes by using the volume method or PV/PVC.

**Prerequisites**

Before using cloud disk data volumes, you must create cloud disks in the Elastic Compute Service \(ECS\) console. For how to create cloud disks, see [Create a cloud disk](../../../../../reseller.en-US/User Guide/Cloud disks/Create a cloud disk.md#).

**Instructions**

-   The cloud disk is not a shared storage and can only be mounted by one pod at the same time.
-   Apply for a cloud disk and obtain the disk ID before using cloud disk storage volumes. See [Create a cloud disk](../../../../../reseller.en-US/User Guide/Cloud disks/Create a cloud disk.md#).
-   volumeId: The disk ID of the mounted cloud disk, which must be the same as volumeName and PV Name.
-   Only the cluster node that is in the same zone as the cloud disk can mount the cloud disk.

**Use volume method**

Use the disk-deploy.yaml file to create the pod.

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-disk-deploy
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-flexvolume-disk
        image: nginx
        volumeMounts:
          - name: "d-bp1j17ifxfasvts3tf40"
            mountPath: "/data"
      volumes:
        - name: "d-bp1j17ifxfasvts3tf40"
          flexVolume:
            driver: "alicloud/disk"
            fsType: "ext4"
            options:
              volumeId: "d-bp1j17ifxfasvts3tf40"
```

**Use PV/PVC**

**Step 1. Create a cloud disk type PV**

You can create the cloud disk type PV in the Container Service console or by using the yaml file.

**Create PV by using yaml file**

Use the `disk-pv.yaml` file to create the PV.

**Note:** The PV name must be the same as the Alibaba Cloud cloud disk ID.

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: d-bp1j17ifxfasvts3tf40
  labels:
    failure-domain.beta.kubernetes.io/zone: cn-hangzhou-b
    failure-domain.beta.kubernetes.io/region: cn-hangzhou
spec:
  capacity:
    storage: 20Gi
  storageClassName: disk
  accessModes:
    - ReadWriteOnce
  flexVolume:
    driver: "alicloud/disk"
    fsType: "ext4"
    options:
      volumeId: "d-bp1j17ifxfasvts3tf40"
```

**Create cloud disk data volumes in Container Service console**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click**Clusters** \> **Storage**in the left-side navigation pane.
3.  Select the cluster from the Clusters drop-down list and then click **Create** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6936/15508185534674_en-US.png)

4.  The Create Data Volume dialog box appears. Configure the data volume parameters.

    -   **Type**: cloud disk in the example.
    -   **Name**: The name of the created data volume. The data volume name must be the same as the **Cloud Disk ID**.
    -   **Access Mode**: ReadWriteOnce by default.
    -   **Cloud Disk ID**: Select the cloud disk to be mounted and is in the same region and zone as the cluster.
    -   **File System Type**: You can select the data type in which data is stored to the cloud disk. The supported types include ext4, ext3, xfs, and vfat ext4 is selected by default.
    -   **Tag**: Click Add Tag to add tags for this data volume.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6936/15508185534675_en-US.png)

5.  After the preceding settings, click **Create**.

**Step 2. Create PVC**

Use the `disk-pvc.yaml` file to create the PVC.

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-disk
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: disk
  resources:
    requests:
      storage: 20Gi
```

**Step 3. Create a pod**

Use the disk-pod.yaml file to create the pod.

```
apiVersion: v1
kind: Pod
metadata:
  name: "flexvolume-alicloud-example"
spec:
  containers:
    - name: "nginx"
      image: "nginx"
      volumeMounts:
        - name: pvc-disk
          mountPath: "/data"
  volumes:
  - name: pvc-disk
    persistentVolumeClaim:
      claimName: pvc-disk
```

## Dynamic storage volumes {#section_t55_yvs_vdb .section}

Dynamic storage volumes require you to manually create a Storage Class and specify the target type of cloud disk in the PVC by storage Class Name.

**Create a StorageClass**

```
kind: StorageClass
apiVersion: storage.k8s.io/v1beta1
metadata:
  name: alicloud-disk-common-hangzhou-b
provisioner: alicloud/disk
parameters:
  type: cloud_ssd
  regionid: cn-hangzhou
  zoneid: cn-hangzhou-b
```

Parameters:

-   provisioner: configured as Alibaba Cloud/disk, the identifier is created using the Alibaba Cloud Provsioner plug-in.
-   type: identifies the cloud disk type, supports cloud, cloud\_efficiency, cloud\_ssd, and available types. To improve efficiency, tries to create SSD, and common cloud disk until it is created successfully.
-   regionid: the region where cloud disk is to be created.
-   zoneid: the zone where cloud disk is to be created.

**Create a service**

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: disk-common
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: alicloud-disk-common-hangzhou-b
  resources:
    requests:
      storage: 20Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: disk-pod-common
spec:
  containers:
  - name: disk-pod
    image: nginx
    volumeMounts:
      - name: disk-pvc
        mountPath: "/mnt"
  restartPolicy: "Never"
  volumes:
    - name: disk-pvc
      persistentVolumeClaim:
        claimName: disk-common
```

**Default options**

By default, the cluster provides the following StorageClasses, which can be used in a single AZ cluster.

-   alicloud-disk-common: basic cloud disk.
-   alicloud-disk-efficiency: high-efficiency cloud disk.
-   alicloud-disk-ssd: SSD disk.
-   alicloud-disk-available: provides highly available options, first attempts to create a high-efficiency cloud disk. If the corresponding AZ's efficient cloud disk resources are sold out, tries to create an SSD disk. If the SSD is sold out, tries to create a common cloud disk.

**Creating a multi-instance StatefulSet using cloud disk**

Use volume Claim Templates that dynamically creates multiple PVCs and PVs and binds them.

```
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  -port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1beta2
kind: StatefulSet
metadata:
  name: web
spec:
  selector:
    matchLabels:
      app: nginx
  serviceName: "nginx"
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: disk-common
          mountPath: /data
  volumeClaimTemplates:
  - metadata:
      name: disk-common
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "alicloud-disk-common"
      resources:
        requests:
          storage: 10Gi
```

