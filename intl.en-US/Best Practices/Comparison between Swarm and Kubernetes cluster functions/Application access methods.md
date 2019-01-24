# Application access methods {#concept_o5f_dsn_jgb .concept}

This topic compares the application access methods used in a Swarm cluster with those used in a Kubernetes cluster. Specifically, these methods are used for access between applications within a cluster, and access between applications outside the cluster and application within the cluster.

## Access applications within a cluster {#section_ind_dtn_jgb .section}

**Container Service Swarm clusters**

For a service name that is to be accessed in a Swarm cluster, you can use the `links` label to set the service name in the container environment variables.

For example, in [YAML files used for creating applications](reseller.en-US/Best Practices/Comparison between Swarm and Kubernetes cluster functions/YAML files used for creating applications.md#), the Web service of the WordPress application is associated with mysql. Therefore, the MySQL service can be accessed through the mysql service name after the container is started.

```
links:			#---7
    - 'db:mysql'
```

**Container Service Kubernetes clusters**

In a Kubernetes cluster, an application can be accessed through the service cluster IP address or the application service name. We recommend that you use service names for access between applications within a Kubernetes cluster.

When creating an application, you can specify the service name that needs to be accessed as an environment variable.

For example, in [YAML files used for creating applications](reseller.en-US/Best Practices/Comparison between Swarm and Kubernetes cluster functions/YAML files used for creating applications.md#), WordPress calls the mysql service through the environmental variable specified in the YAML file of the application.

```
spec:    
      containers:    
      - image: wordpress:4   
        name: wordpress
        env:    
        - name: WORDPRESS_DB_HOST
          value: wordpress-mysql    #---7 Use the mysql service name to specify the MySQL that needs to be accessed.
        - name: WORDPRESS_DB_PASSWORD    
```

## Access applications from outside a cluster {#section_glg_wxs_jgb .section}

**A Swarm cluster application is accessed through a domain name**

**Note:** 

-   You must ensure the network connection status is normal for either a classic network or a VPC.
-   DNS can forward traffic to different backend IP addresses through its load balancing capacity.
-   If a Swarm cluster application is accessed through a domain name, you can migrate the application services from the Swarm cluster to a Kubernetes cluster without downtime.

**Simple routing \(a domain name bound to the default SLB of a Swarm cluster\)**

Create an application in a Kubernetes cluster and verify the application availability is available before migrating a Swarm cluster application to the Kubernetes cluster.

![](images/36481_en-US.png)

**Migration method**

-   Follow these steps to create an application in a Kubernetes cluster:

    -   In the Kubernetes cluster, create an application of the same type as the application that you want to migrate from a Swarm cluster.
    -   In the Kubernetes cluster, create an SLB service for the application.
    -   The SLB service creates an SLB instance. In this example, the IP address of the SLB instance is 2.2.2.2.
    -   Add 2.2.2.2 to the backend IP addresses of the test.com domain name in DNS.
-   Verify that the created application in the Kubernetes cluster is available

    Access the created application through 2.2.2.2 to verify the created application in the Kubernetes cluster is available.

-   Migrate the application

    Remove 1.1.1.1 from the backend IP addresses of the test.com domain name in DNS.


After you complete the preceding steps, all traffic destined for the application in the Swarm cluster is all forwarded by DNS to the Kubernetes cluster application.

**Simple routing \(a domain name specified for an application is bound to an on-premise SLB of a Swarm cluster\)**

In a Swarm cluster, you can bind an application domain name to the default SLB or an on-premise SLB. The differences between these two methods are as follows:

-   The SLB is on-premise and not the default one.
-   By default, the DNS is Alibaba Cloud DNS. If you use your own domain name, you need to manually resolve it.

**Migration method**

You can use the same migration method as that used for the scenario in which the domain name is bound to the default SLB of a Swarm cluster. That is, create an application in a Kubernetes cluster and then verify if the application is available before migrating.

![](images/36492_en-US.png)

**A Swarm cluster application is accessed through <HostIP\>:<port\>**

If a Swarm cluster application is accessed through <HostIP\>:<port\>, the application service migration will encounter downtime. Therefore, we recommend that you migrate the application service when the application has the minimum access traffic.

**Migration method**

1.  Create an application in a Kubernetes cluster and use a NodePort service to expose the access method of the application outside the cluster. For more information, see [Configure port mapping](reseller.en-US/Best Practices/Comparison between Swarm and Kubernetes cluster functions/Network settings used for creating an application through an image.md#section_fbl_gbt_ggb).
2.  Replace the <port\> value of the Swarm cluster with the <NodePort\> value specified for the Kubernetes cluster.

    **Note:** You need to disable and modify the applications in the Swarm cluster one by one.

3.  Mount the Worker nodes in the Kubernetes cluster to the SLB instance in the Swarm cluster.
4.  After you verify that the application in the Kubernetes cluster is available, remove the nodes of the Swarm cluster from the SLB instance in the Kubernetes cluster. Then the application services are migrated from the Swarm cluster to the Kubernetes cluster. Note that before you perform this step, some traffic destined for the application of the Swarm cluster will be forwarded to the application of the Kubernetes cluster.

**An application is accessed through an SLB instance**

If a Swarm cluster application is accessed through an SLB instance, the application service migration will encounter downtime. Therefore, we recommend that you migrate the application services when there is the minimum service traffic.

**Migration method**

In a Kubernetes cluster, you can use an SLB instance in the same way as in a Swarm cluster. For more information, see [Configure Server Load Balancer](reseller.en-US/Best Practices/Comparison between Swarm and Kubernetes cluster functions/Network settings used for creating an application through an image.md#section_wwh_nbt_ggb).

