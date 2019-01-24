# Basic terms {#concept_l4w_qg2_ggb .concept}

This topic compares the basic terms that are used for both Swarm clusters and Kubernetes clusters.

## Application {#section_obd_tk2_ggb .section}

**Container Service Swarm clusters**

In a Container Service Swarm cluster, applications can be viewed as projects. Each application can include multiple services. Each service is an instance that provides the specific function. Services can be horizontally expanded.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83094/154829457535233_en-US.png)

**Container Service Kubernetes clusters**

In a Container Service Kubernetes cluster, an application, also known as a deployment, is used to provide functions. A deployment contains pods and containers. A pod is the minimum resource unit that can be scheduled in Kubernetes and each pod can contain multiple containers. A pod can be viewed as an instance of the application to which the pod belongs. Multiple pods can be scheduled to different nodes. This means that pods can be horizontally expanded.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83094/154829457535234_en-US.png)

**Note:** The preceding figure in which each pod has multiple containers is used to show the expansion capability of pods. However, we recommend that you set only one container for each pod.

## Service {#section_kdx_5q2_ggb .section}

**Container Service Swarm clusters**

Each service in a Container Service Swarm cluster is an instance that provides a specific function. When you create an application in a Swarm cluster, the access method of the service is exposed directly outside the cluster.

**Container Service Kubernetes clusters**

The service term in Container Service Kubernetes clusters is an abstract concept. A service can expose the access method of its application \(or deployment\) outside the cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83094/154829457535237_en-US.png)

## Application access {#section_yfw_5r2_ggb .section}

**Container Service Swarm clusters**

When you deploy an application in a Container Service Swarm cluster, you can select one from three types of application access methods that can directly expose the application. The three types of application access methods are:

-   <HostIP\>:<port\>
-   Simple routing
-   Server Load Balancer \(SLB\)

**Container Service Kubernetes clusters**

After you create an application in a Container Service Kubernetes cluster, you must create a service to expose the access method of the application. Then the application becomes accessible. Applications within a Container Service Kubernetes cluster can then access each other through their service names. Service names are only applicable to the access within the cluster. To access the application from outside the cluster, you need to create a service of the NodePort type or a service of the LoadBalancer type to expose the application.

-   ClusterIP \(It has the same function as a service name. That is, it is applicable to accesses within a cluster.\)
-   NodePort \(It can be viewed as <HostIP\>:<port\> of Swarm clusters.\)
-   LoadBalancer \(It can be viewed as the SLB of Swarm clusters.\)
-   Domain name implemented by creating an Ingress \(It can be viewed as the simple routing of Swarm clusters.\)

