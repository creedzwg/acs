# Prepare TensorFlow training data by using TFRecord and HDFS {#concept_nmg_rll_vdb .concept}

Data preparation and preprocessing play important roles in the deep learning and training process, and affect the speed and quality of model training.

TensorFlow supports the HDFS, integrates big data and deep learning, and completes the chain from data preparation to model training. The deep learning solution of Alibaba Cloud Container Service provides three distributed storage backends \(Object Storage Service \(OSS\), NAS, and HDFS\) to support TensorFlow.

This document describes how to convert data to the TFRecord format and store the generated TFRecord files to the HDFS. The HDFS of Alibaba Cloud Elastic MapReduce \(E-MapReduce\) is used in this example.

## Why is TFRecord used {#section_c4q_5ll_vdb .section}

TFRecord is the unified standard data format defined in TensorFlow. It supports multithreading data read and uses the batch size and epoch parameters to control the size of a single batch and the number of iterations of the sample file during the training process. TFRecord can also make better use of the memory and easily perform data replication and movement. Therefore, it is the preferred option for TensorFlow to perform large-scale deep learning training.

## Step 1 Create an E-MapReduce cluster {#section_d4q_5ll_vdb .section}

E-MapReduce is a big data processing system solution running on Alibaba Cloud platform. For more information, see [E-MapReduce overview](https://www.alibabacloud.com/help/doc-detail/28068.htm?spm=a2c63.p38356.879954.6.e7b64c2cTZPBjr&spm).

Log on to the [E-MapReduce console](https://partners-intl.aliyun.com/login-required#/emr) to create an E-MapReduce cluster. For how to create an E-MapReduce cluster, see [Create an E-MapReduce cluster](https://www.alibabacloud.com/help/doc-detail/28088.htm?spm=a2c63.p38356.879954.8.e7b64c2cTZPBjr).

In this example, a cluster located in China South 1 \(Shenzhen\) is created, and **Network Type** is set to **VPC**.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7441/15613704792172_en-US.png)

## Step 2 Create container clusters and integrate networks between the two clusters {#section_vmt_1ml_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs) and create a GPU container cluster under the same Virtual Private Cloud \(VPC\).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7441/15613704792175_en-US.png)

2.  Log on to the [ECS console](https://partners-intl.console.aliyun.com/#/ecs) and add nodes of the Container Service cluster to the security group corresponding to the E-MapReduce cluster.
    1.  Select the region in which the security group resides \(China South 1 \(Shenzhen\) in this example\). Click **Manage Instances** at the right of the security group.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7441/15613704792176_en-US.png)

    2.  Click **Add an ECS Instance** in the upper-right corner. Select a node in the container cluster and click **OK**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7441/15613704792177_en-US.png)


## Step 3 Generate TFRecord data {#section_k4q_5ll_vdb .section}

In this example, the model training service provides a running environment to run convert\_to\_records.py, generate TFRecord data, and store the data in the HDFS.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click **Images and Templates \>** \> **Solutions**in the left-side navigation pane.
3.  Click **Launch** in **Training**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7441/15613704792178_en-US.png)

4.  Configure the settings for model training and click **OK**.

    Configurations in this example are as follows:

    -   **Framework**: Select **tensorflow:1.0.0**.
    -   **GPUs Per Worker**: Enter 0.
    -   **Data Source**: Select No Data Source.
    -   **Git URL**: Enter https://code.aliyun.com/deeplearning/mnist-examples.git.
    -   **Command:** 

        ``` {#codeblock_e0c_e1k_urd}
        python neural_style.py --iterations 50000 --content
                      /neural-style/examples/1-content.jpg --styles /neural-style/examples/1-style.jpg
                      --output /neural-style/output.jpg
        ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7441/15613704792179_en-US.png)

    Then, the created application is displayed on the Application List page. Click the application name. Click the Logs tab and view the execution logs, which indicates the TFRecord files are stored in the HDFS.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7441/15613704792180_en-US.png)

    You can log on to the E-MapReduce machine to check the generated TFRecord files.

    ``` {#codeblock_7dn_1i9_xwr}
    # hdfs dfs -ls /mnist-tfrecord
    SLF4J: Class path contains multiple SLF4J bindings.
    SLF4J: Found binding in [jar:file:/opt/apps/hadoop-2.7.2/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar! /org/slf4j/impl/StaticLoggerBinder.class]
    SLF4J: Found binding in [jar:file:/opt/apps/tez-0.8.4/lib/slf4j-log4j12-1.7.10.jar! /org/slf4j/impl/StaticLoggerBinder.class]
    SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
    SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
    Found 3 items
    -rw-r--r-- 3 root hadoop 8910000 2017-05-23 19:34 /mnist-tfrecord/test.tfrecords
    -rw-r--r-- 3 root hadoop 49005000 2017-05-23 19:33 /mnist-tfrecord/train.tfrecords
    -rw-r--r-- 3 root hadoop 4455000 2017-05-23 19:33 /mnist-tfrecord/validation.tfrecords
    ```


