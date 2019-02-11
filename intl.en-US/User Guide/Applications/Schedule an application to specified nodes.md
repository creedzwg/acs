# Schedule an application to specified nodes {#concept_gg1_2vs_xdb .concept}

To deploy an application to specified nodes, we recommend that you use user tags and the `constraint` keyword to make the deployment configurations.

**Note:** 

-   The deployment constraint only works for newly created containers. It does not work when existing containers change the configurations.
-   After you use a user tag to deploy an application, deleting the user tag does not affect the deployed application, but will affect the next deployment of the application. Proceed with caution when deleting user tags.

## Procedure {#section_apr_gvs_xdb .section}

1.  Add user tags for nodes.
    1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    2.  Click Swarm \> **Clusters** in the left-side navigation pane.
    3.  Click **Manage** at the right of the cluster.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7050/15498664224966_en-US.png)

    4.  Click **User Tags** in the left-side navigation pane.
    5.  Select the nodes that you want to deploy the application and then click **Add Tag**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7050/15498664224967_en-US.png)

    6.  Enter your tag key and tag value, and then click **OK** to add user tags for the selected nodes.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7050/15498664224968_en-US.png)

2.  Create an application by clicking **Create with Orchestration Template**. Configure the `constraint` keyword in the template.

    For information about how to create an application, see [Create an application ](reseller.en-US/User Guide/Applications/Create an application .md#).

    ```
    environment:
      - constraint:group==1 #Indicates to deploy the application on all the nodes with the "group:1" tag
    ```


## Delete a user tag {#section_wmk_gvs_xdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click Swarm \> **Clusters** in the left-side navigation pane.
3.  Click **Manage** at the right of the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7050/15498664224966_en-US.png)

4.  Click **User Tags** in the left-side navigation pane.
5.  Select the nodes that you want to delete the user tags and then click **Delete Tag**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7050/15498664224969_en-US.png)

6.  The confirmation dialog box appears. Click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7050/15498664224970_en-US.png)


