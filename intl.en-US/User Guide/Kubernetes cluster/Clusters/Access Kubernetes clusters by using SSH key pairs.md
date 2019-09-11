# Access Kubernetes clusters by using SSH key pairs {#task_hq3_4cr_b2b .task}

Alibaba Cloud Container Service allows you to log on to clusters by using SSH key pairs, which guarantees the security of SSH remote access.

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/). 
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane. 
3.  Click **Create Kubernetes Cluster** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14698/15681844766173_en-US.png)

4.  Select Key Pair in the Login field. Complete the other configurations. For more information, see [Create a cluster](intl.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#). Then, click **Create**. 

    1.  If you have created key pairs in the Elastic Compute Service \(ECS\) console, select a key pair from the Key Pair Name drop-down list.
    2.  If you have no key pair, click **Create a new key pair** to create one in the ECS console. For more information, see [Create an SSH key pair](../../../../intl.en-US/Security/Key pairs/Use an SSH key pair.md#).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14698/15681844766174_en-US.png)

5.  After the cluster is created, click **Manage** at the right of the cluster on the Cluster List page. View the **Master node SSH IP address** under Connection Information. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14698/15681844766176_en-US.png) 

6.  Download the .pem private key file. Complete the configurations based on your local operating system environment, such as Windows or Linux. For more information, see [Connect to a Linux instance by using an SSH key pair](../../../../intl.en-US/Instances/Connect to instances/Connect to Linux instances/Connect to a Linux instance by using an SSH key pair.md#). Take Linux as an example. 
    1.  Find the path where your downloaded .pem private key file is stored on your local machine. For example, `/root/xxx.pem`. 
    2.  Run the following command to modify the attributes of the private key file: `chmod 400 [path where the .pem private key file is stored on the local machine]`. For example, `chmod 400 /root/xxx.pem`. 
    3.  Run the following command to connect to the cluster: ``ssh -i [path where the .pem private key file is stored on the local machine] root@[master-public-ip]`. Wherein, master-public-ip is the master node SSH IP address. For example, `ssh -i /root/xxx.pem root@10.10.10.100`. 

