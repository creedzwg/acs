# Implement multiple environments by using configurations {#concept_wbl_zct_xdb .concept}

An application consists of codes and configurations. After an application is containerized, the configurations are usually transmitted by using container environment variables to deploy multiple applications using the same image and different configurations.

## Limits {#section_wnn_cdt_xdb .section}

-   When associating a configuration file with an application, make sure the configuration file is in the same region as the application.
-   Currently, associating a configuration file when creating an application is only available when you create the application by using an orchestration template.

## Create an application {#section_w5x_ddt_xdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Swarm, click **Configurations** in the left-side navigation pane. Select the region in which you want to create a configuration from the Region list and click **Create**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7063/15644529095003_en-US.png)

3.  Complete the settings and then click **OK**.

    -   **File Name**: It can contain 1–32 characters.
    -   **Description**: It can contain up to 128 characters.
    -   **Configuration**: You can add up to 50 configurations in a region.
    In this example, the `size` variable is set.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7063/15644529095005_en-US.png)

4.  Under Swarm, click **Applications** in the left-side navigation pane. Select the cluster in the same region as the created configuration from the Cluster list and click **Create Application**.
5.  Enter the basic information of the application and click **Create with Orchestration Template**.
6.  Enter the following orchestration template and then click **Create and Deploy**.

    Wherein, `size` is a dynamic variable and will be overwritten by the value in the configuration.

    ``` {#codeblock_vjd_d0t_bpj}
    busybox:
     image: 'busybox'
     command: 'top -b'
     labels:
         aliyun.scale: $size
    ```

7.  The dialog box appears. Select the configuration file to be associated with from the **Associated Configuration File** drop-down list. Click Replace Variable and click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7063/15644529095006_en-US.png)


## Update an application {#section_ecp_1dt_xdb .section}

If you associated a configuration file when creating an application, you can update the application by modifying the configuration file and redeploying the application.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Swarm, click **Configurations** in the left-side navigation pane. Select the region in which the configuration you want to modify resides from the Region list, and click **Modify** at the right of the configuration.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7063/15644529095007_en-US.png)

3.  Click **Confirm** in the displayed dialog box.
4.  Click **Edit** \(changes to **Save** after you click it\) at the right of the variable you want to modify. Modify the variable value. Click **Save** and then click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7063/15644529095008_en-US.png)

5.  Under Swarm, click **Applications** in the left-side navigation pane. Select the cluster in the same region as the created configuration, and then click **Redeploy** at the right of the application.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7063/15644529105009_en-US.png)

    After the application is updated, the number of containers changes to three.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7063/15644529106143_en-US.png)


## Trigger an update {#section_kcp_1dt_xdb .section}

If you associated a configuration file when creating an application, you can redeploy the application by using the redeployment trigger.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Swarm, click **Configurations** in the left-side navigation pane. Select the region in which the configuration you want to modify resides from the Region list, and click **Modify** at the right of the configuration.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7063/15644529105014_en-US.png)

3.  Click **Confirm** in the displayed dialog box.
4.  Click **Edit**\(changes to **Save** after you click it\) at the right of the variable you want to modify. Modify the variable value. Click **Save** and then click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7063/15644529095008_en-US.png)

5.  Create a redeployment trigger.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7063/15644529105015_en-US.png)

6.  Initiate the redeployment trigger.

    ``` {#codeblock_idg_gm6_6uj}
    curl "https://cs.console.aliyun.com/hook/trigger?triggerUrl=Y2ViZDhkZTIwZGMyMjRmOTM4NDIzMTgwMzI3NmIwM2IxfHRlc3QtZ3JvdXB8c2NhbGluZ3wxOXZwYzNmOXFiNTcwfA==&secret=466242376775654951546d6451656a7a66e7f5b61db6885f8d15aa64826672c2"
    ```

    After the application is updated, the number of containers changes to three.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7063/15644529116144_en-US.png)


