# Enable Log Service {#concept_bff_xst_xdb .concept}

Log Service is a platform service for log scenarios. You can collect, distribute, ship, and query logs quickly without development, which is applicable to scenarios such as log transfer, monitoring, performance diagnosis, log analysis, and audit. Container Service integrates with Log Service, which allows you to send the application logs to Log Service.

**Note:** On the cluster management page, choose **Enable Log Service** \> **OK**. After Log Service is successfully enabled, the log index is created for each automatically created Logstore by using the built-in Resource Access Management \(RAM\) account. With this feature enabled, you are charged for the Alibaba Cloud Log Service usage after configuring the following settings. For more information, see [Billing method](../../../../reseller.en-US/Pricing/Billing method.md#). Make sure you know your log volume to avoid large unexpected costs.

## Enable Log Service {#section_abr_dtt_xdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click **Clusters** in the left-side navigation pane.
3.  Click **Manage** at the right of the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7085/15588640305270_en-US.png)

4.  Click **Enable Log Service** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7085/15588640305271_en-US.png)

5.  In the dialog box, click **OK**.

    Before enabling Log Service in Container Service, activate the RAM service and Log Service first. Click **Activate It** to activate the RAM service and Log Service if they are not activated yet. The created Log Service project is displayed after Log Service is successfully enabled.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7085/15588640305272_en-US.png)


## Check installation result of acslogging service {#section_u5w_ctt_xdb .section}

Container Service installs the Agent required by Log Service on your machine if this is the first time Log Service is enabled.You can use Log Service after the application is installed successfully. You can find this application on the Application List page. You can use Log Service after the application is installed successfully.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click **Applications** in the left-side navigation pane.
3.  Select the cluster from the Cluster list and clear the **Hide System Applications** check box.

    The acslogging application is successfully installed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7085/15588640305273_en-US.png)


The system creates a corresponding project in Alibaba Cloud Log Service. You can view the project in the Log Service console. The project name contains the Container Service cluster ID.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7085/15588640305274_en-US.png)

## Use Log Service in orchestration files {#section_y5w_ctt_xdb .section}

Most Docker applications write the logs directly to stdout, now you can do this as well \(for the scenarios of writing logs to files, see [Use file logs](#section_kvw_ctt_xdb) in the following section\). After enabling Log Service, stdout logs are automatically collected and sent to Alibaba Cloud Log Service.

In the following example a WordPress application is created. It contains two services: WordPress service and MySQL service. Logs are collected to Alibaba Cloud Log Service. which contains two services: WordPress service and MySQL service. Logs are collected to Alibaba Cloud Log Service.

**MySQL and WordPress**

``` {#codeblock_4b9_ep4_xmi}
mysql:
  image: mysql
  ports:
      - 80
  labels:
      aliyun.scale: "1"
  environment:
      - MYSQL_ROOT_PASSWORD=password
web:
  image: registry.aliyuncs.com/jiangjizhong/wordpress
  ports:
     - 80
  labels:
     aliyun.routing.port_80: wordpress-with-log
     aliyun.log_store_dbstdout: stdout  # Collect stdout logs to the dbstdout Logstore.
     aliyun.log_ttl_dbstdout: 30  # Set the data retention time for the dbstdout Logstore to 30 days.
  links:
      - mysql
```

In the preceding orchestration file:

-   `aliyun.log_store_dbstdout: stdout` indicates to write the container standard to the Logstore `acslog-wordpress-dbstdout`. The label format is `aliyun.log_store_{name}: {logpath}`。 Wherein:
    -   `name` is the name of the Alibaba Cloud Log Service Logstore. The actually created Logstore name is `acslog-${app}-${name}`.
    -   `app` is the application name.
    -   `logpath` is the log path in the container.
    -   `stdout` is a special `logpath`, indicating the standard output.
-   `aliyun.log_ttl_<logstore_name>` is used to set the data retention time \(in days\) for the Logstore. The value range 1–365. If left empty, logs are kept in the Logstore for two days by default.

    **Note:** The value configured here is the initial configuration value. To modify the data retention time later, modify it in the Log Service console.


You can create an application named `wordpress` in the Container Service console by using the preceding orchestration file. After the application is started, you can find the Logstore `acslog-wordpress-dbstdout` in the Log Service console, in which stores the logs of application `wordpress`.

## View logs in Log Service console {#section_hvw_ctt_xdb .section}

After deploying an application by using the preceding orchestration file, you can view the collected logs in the Alibaba Cloud Log Service console. Log on to the Log Service console. Find the Log Service project corresponding to the cluster. You can view the Logstore `acs-wordpress-dbstdout` used in the orchestration file.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7085/15588640305275_en-US.png)

Click **Search** at the right of the Logstore to view the logs.

## Use file logs {#section_kvw_ctt_xdb .section}

To write the logs directly to files \(for example, /var/log/app.log\) instead of stdout, configure as follows:

```
aliyun.log_store_name: /var/log/app.log
```

`name` is the Logstore name. /var/log/app.log is the log path in the container.

To output multiple log files to Log Service, configure as follows to put the files under multiple directories:

```
aliyun.log_store_s1: /data/logs/access/access.log
aliyun.log_store_s2: /data/logs/error/error.log
aliyun.log_store_s3: /data/logs/exception/*.log #Wildcards are supported
```

**Note:** Currently, multiple Logstores cannot correspond to the same log directory. The log files corresponding to the three Logstores s1, s2, and s3 in the preceding example must be under three directories.

## Enable timestamp {#section_nvw_ctt_xdb .section}

You can select whether to add timestamp when Docker is collecting logs. Configure timestamp by using the `aliyun.log.timestamp` label in Container Service. label in Container Service. The timestamp is added by default.

-   Add timestamp

    `aliyun.log.timestamp: "true"`

-   Remove timestamp

    `aliyun.log.timestamp: "false"`


