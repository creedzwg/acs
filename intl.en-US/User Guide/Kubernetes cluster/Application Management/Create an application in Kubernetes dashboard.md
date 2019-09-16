# Create an application in Kubernetes dashboard {#task_cpq_w4l_vdb .task}

You can create an application in the Kubernetes dashboard.

1.   Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.   Under Kubernetes, click **Clusters** in the left-side navigation pane. 
3.   Click **Dashboard** at the right of the cluster to enter the Kubernetes dashboard. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6894/15686024924370_en-US.png)

4.   In the Kubernetes dashboard, click **CREATE** in the upper-right corner to create an application. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6894/15686024924371_en-US.png)

5.   The Resource creation page appears. Configure the application information. 

    Create an application in any of the following three ways:

    -   **CREATE FROM TEXT INPUT:** Directly enter the orchestration codes in the YAML or JSON format to create an application. You must know the corresponding orchestration format.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6894/15686024924372_en-US.png)

    -   **CREATE AN APP:** Complete the following configurations to create an application.

        -   **App name**: Enter the name of the application you are about to create. In this example, enter `nginx-test`.
        -   **Container image**: Enter the URL of the image to be used. In this example, use Docker [Nginx](https://hub.docker.com/_/nginx/).
        -   **Number of pods**: Configure the number of pods for this application.
        -   **Service**: Select **External** or **Internal**. **External** indicates to create a service that can be accessed from outside the cluster. **Internal** indicates to create a service that can be accessed from within the cluster.
        -   **Advanced options**: To configure the information such as labels and environment variables, click **SHOW ADVANCED OPTIONS**. This configuration distributes the traffic load evenly to three pods.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6894/15686024924373_en-US.png)

    -   **CREATE FROM FILE:** Upload an existing YAML or JSON configuration file to create an application.
6.   Click UPLOAD or **DEPLOY** to deploy the containers and services. 

    You can also click **SHOW ADVANCED OPTIONS** to configure more parameters.


After clicking UPLOAD or **DEPLOY**, you can view the services and containers of the application.

Click **Pods** in the left-side navigation pane. You can check the status of each Kubernetes object according to the icon on the left.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6894/15686024926116_en-US.png) indicates the object is still being deployed. ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6894/15686024926118_en-US.png)indicates the object has completed the deployment.

 ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6894/15686024924374_en-US.png) 

