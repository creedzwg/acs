# Create an application {#task_z4x_nyr_xdb .task}

**Limits**

Swarm clusters only support the compose V1 and compose V2 orchestration templates. The system reports an error if you select to use the compose V3 template.

**Note:** In the orchestration template list, compose V3 templates are marked with `composev3`.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click **Applications** in the left-side navigation pane.
3.  Click **Create Application** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7045/15616169224949_en-US.png)

4.  Complete the basic information for the application you are about to create. 

    -   **Name**: Enter the name of the application. It can be 1–64 characters long and contain numbers, English letters, and hyphens \(-\), but cannot start with a hyphen \(-\).
    -   **Version**: Enter the version of the application. By default, 1.0 is entered.
    -   **Cluster**: Select the cluster on which the application is to be deployed.
    -   **Update**: The update method of the application. Select **Standard Release** or **Blue-Green Release**. For more information, see [Introductions on release strategies](reseller.en-US/User Guide/Release policy/Introductions on release strategies.md#).
    -   **Description**: Enter the information of the application. This field is optional. The entered description cannot exceed 1024 characters, and is displayed on the Application List page.
    -   **Pull Docker Image**: With this check box selected, Container Service pulls the latest Docker image from the repository to deploy the application, even when the image tag does not change.

        To improve efficiency, Container Service caches the image. When deploying an application, Container Service uses the cached image instead of pulling the image from the repository if the image tag is the same as that of the local cache. Therefore, if you modify your codes and image but do not modify the image tag for the convenience of upper business, Container Service uses the old image cached locally to deploy the application. With this check box selected, Container Service ignores the cached image and re-pulls the image from the repository when deploying the application to make sure the latest image and codes are always used.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7045/15616169224950_en-US.png)

5.  Click **Create with Image**. 

    Click **Create with Image**. Set the following parameters according to your requirements.

    1.  In the General section: 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7045/15616169224951_en-US.png)

        -   Set the **Image Name** and **Image Version**.

            You can select an image provided by Container Service or enter your image address in the format of `domainname/namespace/imagename:tag`. To select an image, click **Select image**, select the image, and then click **OK**. By default, the Container Service uses the latest image version. To use another version of the image, click **Select image version**, and then click **OK**.

        -   Set the number of containers \(**Scale**\).
        -   Select the **Network Mode** of the application. Currently, Container Service supports two network modes: **Default** and **host**. The Default mode is the bridge network mode. The host network mode allows containers to use the network stacks of Elastic Compute Service \(ECS\) instances. For more information, see [Docker container networking](https://docs.docker.com/engine/userguide/networking/).
        -   Set the **Restart** field.

            The Always check box is selected by default. With the check box selected, the containers are restarted regardless of the exit status code. Docker daemon restarts the containers unlimitedly. Whatever the container status is, the container tries to be restarted when daemon is started.

            When the check box is not selected, the restart policy becomes no, indicating containers are not restarted automatically on exit.

    2.  In the Container section: 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7045/15616169224952_en-US.png)

        -   Set the startup command \(**Command** and **Entrypoint**\) of the container. If configured, the image default configurations are overwritten.

            Command is used to specify the startup command of the container main process. For more information, see [Command](https://docs.docker.com/engine/reference/builder/#cmd).

            Entrypoint is used to specify the container startup process and parameter. Used together with command, the cmd contents can be passed to Entrypoint as parameters. For more information, see [Entrypoint](https://docs.docker.com/engine/reference/builder/#entrypoint).

        -   Set the resource limits \(**CPU Limit** and **Memory Limit**\) of the container.

            Set the resource limit for the CPU and memory to be used by the container. For more information, see [Restrict container resources](reseller.en-US/User Guide/Applications/Restrict container resources.md#).

        -   Set the **Capabilities**.

            For how to add or drop Linux related privileges for the container, see [Capabilities](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities).

        -   Set the **Container Config**.

    3.  In the Network section: 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7045/15616169224953_en-US.png)

        -   Set the **Port Mapping**. Specify the port mapping for the host and the container, and select TCP or UDP as the protocol.

            The port mapping is used for the routing between container and host, and is the precondition of Web Routing and Load Balancer. The container provides external services by means of the configured port mapping.

        -   Set the **Web Routing**. The cluster automatically creates the acsrouting application, including the routing service, and provides the simple routing function. A routing service instance is deployed on each node. In a node, the `acsrouting_routing_index` container implements the routing forward in the cluster to route the HTTP or HTTPS service. For more information, see [Simple routing - supports HTTP and HTTPS](reseller.en-US/User Guide/Service discovery and load balancing/Simple routing - supports HTTP and HTTPS.md#).

            **Note:** When exposing the HTTP/HTTPS services, you can use the overlay network or Virtual Private Cloud \(VPC\) to directly access the container port, without configuring the specific host port.

        -   Set the **Load Balancer**. Configure the port mapping before configuring the mapping of `container_port``$scheme://$[slb_name|slb_id]:$slb_front_port` . For how to use the Server Load Balancer label, see [lb](reseller.en-US/User Guide/Service orchestrations/lb.md#).

            When configuring this parameter, control the routing access path on your own, including the routing mapping of Server Load Balancer front end port \> backend host port \> container port.

    4.  Set the **Data Volume**. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7045/15616169234954_en-US.png)

        -   Create a data volume. Enter the host path or data volume name, the container path, and select RW or RO as the data volume permission. For more information, see [volume](https://docs.docker.com/compose/compose-file/compose-file-v2/#volumes-volume_driver).
        -   Configure the volumes\_from field. Enter the name and permission parameter of another service or container, such as `service_name:ro`. If no access permission is specified, RW is the default permission. For more information, see [volumes\_from](https://docs.docker.com/compose/compose-file/compose-file-v2/#volumes_from). After the configuration, the container is authorized to use volumes of another service or container.
    5.  Set the **Environment variables**. Formats such as array, dictionary, and boolean are supported. For more information, see [Environment variables](https://docs.docker.com/compose/environment-variables/).
    6.  Set the container **Labels**. 

        For the extension labels supported by Container Service, see [Label description](reseller.en-US/User Guide/Service orchestrations/Label description.md#).

    7.  In the Deploy section: 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7045/15616169234955_en-US.png)

        -   Set whether to enable **Smooth Upgrade** for containers.

            For more information, see [Rolling\_updates](reseller.en-US/User Guide/Service orchestrations/Rolling_updates.md#).

        -   Set the **Across Multiple Zones** settings for containers.

            Select **Ensure** to deploy containers in two zones. The container creation fails if less than two zones are in the current cluster, or the containers cannot be deployed in two zones because of limited machine resources. Select **Try best** to try to deploy containers in two zones. The container can still be created even if this condition is not met.

            If this parameter is not configured, Container Service deploys the containers in one zone by default. For more information, see [High availability scheduling](reseller.en-US/User Guide/Service orchestrations/High availability scheduling.md#).

        -   Set whether or enable the container **Auto Scaling**.

            For more information, see [Container auto scaling](reseller.en-US/User Guide/Monitoring/Container auto scaling.md#).

    8.  Click **Create** at the right of the page after completing the settings.
6.  Click **Create with Orchestration Template**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7045/15616169234958_en-US.png)

    1.  Click **Use Existing Orchestration Template** or write a new template by yourself. 

        The contents of the orchestration template comply with the Docker Compose format.

    2.  Click **Select** next to the template after clicking Use Existing Orchestration Template.
    3.  Edit the orchestration template. 

        Edit the orchestration template according to your requirements. Make modifications in the template directly, or click **Edit** to modify the service or **Delete** to delete the service.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7045/15616169234960_en-US.png)

        Click **Add Service** to add another service to this orchestration template. Select the image and configure the parameters. Then, click **OK**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7045/15616169234961_en-US.png)

    4.  Click **Create and Deploy** after completing the settings.

