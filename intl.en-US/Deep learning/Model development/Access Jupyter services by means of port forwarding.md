# Access Jupyter services by means of port forwarding {#concept_und_p5g_vdb .concept}

Accessing Jupyter services by means of port forwarding has the following advantages. However, the settings are complicated.

-   Save your cost because no Server Load Balancer instance needs to be purchased.
-   You can access Jupyter services from outside without opening a port on the official website.

In the following example, your Jupyter services are SSH accessed by forwarding the access request by means of the local port 12345.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15616160172130_en-US.png)

## Step 1 Set up the SSH tunnel {#section_kqj_dvg_vdb .section}

**In MAC OS X and Linux**

Run the following command to connect the local port with the Elastic Compute Service \(ECS\) instance.

``` {#codeblock_2yn_nru_voy}
ssh -ND 12345 root@39. 252
```

`12345` is the local port to be used, which can be customized. `39. 252` is the public Elastic IP \(EIP\) bound to your ECS instance.

**Note:** You can log on to the [ECS console](https://partners-intl.console.aliyun.com/#/ecs) to check the public EIP bound to the ECS instance. If your ECS instance is not bound with a public EIP, bind one to your ECS instance. For more information, see [../../SP\_73/DNEIP11899563/EN-US\_TP\_12805.dita\#task\_bh5\_dll\_vdb](../../SP_73/DNEIP11899563/EN-US_TP_12805.dita#task_bh5_dll_vdb).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15616160172131_en-US.png)

**In Windows**

1.  Configure local port forwarding.

    Run PuTTY and configure the SSH **Tunnels**.

    1.  Set **Source port**. In this example, it is 12345.
    2.  Select **Dynamic**.
    3.  Click **Add**.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15616160172132_en-US.png)

2.  Log on to the ECS instance.
    1.  Run PuTTY. Configure the **Session**, and then click **Open**.

        Enter the IP address, namely, the public EIP of the ECS instance. In this example, it is 39. 252.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15616160172133_en-US.png)

    2.  In the displayed dialog box, enter your logon account and password for the ECS instance.

        Then, you have successfully logged on to the ECS instance.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15616160172134_en-US.png)


## Step 2 Configure network connection of the browser {#section_dcn_1wg_vdb .section}

**For Firefox**

If you use the Firefox browser, open the browser, click **Tools** \> **Options** \> **Advanced** \> **Network** , and click **Settings** in the **Connection** section. In the displayed window, set the **SOCKS Host**.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15616160182135_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15616160182136_en-US.png)

**For Chrome**

Run the following command:

``` {#codeblock_px7_zab_qq1}
Chrome --proxy-server="socks5://localhost:12345" --host-resolver-rules="MAP * 0.0.0.0 , EXCLUDE localhost" --user-data-dir=/tmppath/
```

**Wherein:**

-   `12345` is the local port in use.
-   For Windows, `/tmppath/` can be written as `d:/tmppath` or a similar path. For Linux or MAC OS X, /tmppath/ can be written as `/tmp/`.

The location of Chrome varies with operating systems, as described in the following table.

|Operating system|Location of Chrome|
|:---------------|:-----------------|
|Mac OS X|/Applications/Google\\ Chromapp/Contents/MacOS/Google\\ Chrome|
|Linux|/usr/bin/google-chrome|
|Windows|C:\\Program Files \(x86\)\\Google\\Chrome\\Application\\chrome.exe|

Take Windows as an example.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15616160182137_en-US.png)

## Step 3 Access Jupyter services {#section_z4c_x5g_vdb .section}

In your browser, enter the access address of a Jupyter service. In this example, access the address 192. 83:32769. Then, you can access the Jupyter service by using SSH from outside.

**Firefox:**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15616160182138_en-US.png)

**Chrome:**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15616160182139_en-US.png)

