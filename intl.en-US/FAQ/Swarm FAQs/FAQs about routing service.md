# FAQs about routing service {#concept_q5k_3wn_vdb .concept}

**Q: How to deploy an accessible Web container in Container Service in a simple and fast way?**

**A:** See [Create an Nginx webserver from an image](../../../../reseller.en-US/Quick Start/Create an Nginx webserver from an image.md#) and [Create WordPress with an orchestration template](../../../../reseller.en-US/Quick Start/Create WordPress with an orchestration template.md#) for how to deploy an application in a simple and fast way.

**Q: How to use the routing service?**

**A：** See [Simple routing - supports HTTP and HTTPS](../../../../reseller.en-US/User Guide/Service discovery and load balancing/Simple routing - supports HTTP and HTTPS.md#).

**Q: How to use the routing label?**

**A:** See the service orchestration document [Label description](../../../../reseller.en-US/User Guide/Service orchestrations/Label description.md#).

**Q: How to access the contents of a deployed Web container?**

**A:** A container is a process or a group of processes. You can access contents in a container only when the process exposes a port. The container abstracts a container port and maps the container port to the host port. You can access the container through the host port that is mapped to the container port.

**Q: For high availability purpose, only one access endpoint is provided for multiple containers with the same functions. How is this implemented in Container Service?**

**A:** As shown in the following figure, a routing service is provided, which can be configured by **Service** \> **Update** \> **Web Routing.** By default, the routing service deploys a routing container on each node in the cluster \(the routing container is displayed in the container list after the cluster is created and belongs to the acsrouting application\). A Server Load Balancer instance is created by default after a cluster is created. All access requests go through the frontend port 80 of the cluster Server Load Balancer \> the node 9080 port \> the port 80 of the routing container. The underlying implementation of the routing container is an HAProxy Server Load Balancer software, which is similar to Nginx, and provides the Server Load Balancer function. The routing container forwards the access requests to different container backends based on the domain names specified by the “HOST” header in HTTP \(containers in the same cluster are interworking\). During routing configuration, pay attention to the differences and relations among the Server Load Balancer ports, the node virtual machine \(VM\) ports, and the container ports.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7374/15681842922442_en-US.png)

**Q: How to add a domain name to a service exposed to the public network and enable the service to support HTTP?**

**A:** See [Simple routing - configure domain names](../../../../reseller.en-US/User Guide/Service discovery and load balancing/Simple routing - Configure domain names.md#).

**Q: How to change the protocol from HTTP to HTTPS?**

**A:** See [Simple routing - change HTTP to HTTPS](../../../../reseller.en-US/User Guide/Service discovery and load balancing/Simple routing - Change HTTP to HTTPS.md#).

**Q: How to redirect an HTTP request to an HTTPS request?**

**A:** Distinguish whether the request protocol is HTTP or HTTPS. If the protocol is HTTP, the request is redirected to HTTPS by 301 or 302. However, when the HTTP request and HTTPS request are forwarded to the same backend port \(for example, port 9080\) by using the ports 80 and 443 of Server Load Balancer respectively, the backend container cannot distinguish whether the request protocol is HTTP or HTTPS. To solve this issue, expose an additional port \(for example, port 8080\) to make the HTTP request go through the following link: the frontend port 80 of the Server Load Balancer \> port 8080 of the node host VM \> a container dedicated for redirect, for example, the Nginx port 80 \> redirected to HTTPS by 302.

**Q: Why are exceptions reported on the HTTP port of Server Load Balancer?**

**A:** Exceptions are reported when the health check on the HTTP port of Server Load Balancer fails. The health check principle is to send an HTTP HEAD request, which is similar to a GET request, but only the response header needs to be returned. The domain name needs to be configured for the HTTP request, and the default value is the IP address. Server Load Balancer considers the health check as successful when the request returns the status code 200. Bypass Server Load Balancer and check whether the request returns the status code 200 by running the [curl](https://curl.haxx.se/) command directly on your host node.

**Q: Why are exceptions reported on the HTTPS port of Server Load Balancer?**

**A:** Exceptions are reported when the health check on the HTTPS port of Server Load Balancer fails. The health check principle is to send an HTTP HEAD request, which is similar to a GET request, but only the response header needs to be returned. The domain name needs to be configured for the HTTP request, and the default value is the IP address. Server Load Balancer considers the health check as successful when the request returns the status code 200. For an HTTPS port, the request domain name must be configured when you configure the health check. Otherwise, the health check fails by default \(the request of the default IP address is forwarded to the routing container, but the routing container does not know to which backend the request is to be forwarded, and error 503 is returned\). Bypass Server Load Balancer and check whether the request returns the status code 200 by running the [curl](https://curl.haxx.se/) command directly on your host node. Check the application validity if the status code 200 is not returned.

**Q: Do clusters support binding or unbinding intranet Server Load Balancer instances?**

**A:** The cluster can bind at most one Server Load Balancer instance and the Server Load Balancer instance can be unbound from the cluster. For more information, see [Bind and unbind a Server Load Balancer instance](../../../../reseller.en-US/User Guide/Clusters/Bind and unbind a Server Load Balancer instance.md#).

**Q: Do clusters support binding multiple cluster-level Server Load Balancer instances?**

**A:** Not supported currently. You can manually create a Server Load Balancer instance and then bind it to port 9080 of the cluster node. When the node is expanded, you must maintain the backend server of your created Server Load Balancer instance on your own, for example, increasing or reducing the number of backend servers.

**Q: How do containers communicate with each other in a cluster?**

**A:** The container name can be used as the internal domain name when a container accesses another container in the same cluster.

**Q: How are service discovery and Server Load Balancer between containers in the same cluster implemented?**

**A:** The routing service proxy is used for the forwarding and discovery. See [Routing and Server Load Balancer between services in a cluster](../../../../reseller.en-US/User Guide/Service discovery and load balancing/Routing and Server Load Balancer between services in a cluster.md#).

**Q: How to troubleshoot problems about routing service access links?**

**A:** See [How to troubleshoot access link issues?](reseller.en-US/FAQ/Swarm FAQs/How to troubleshoot access link issues?.md#).

**Q: The Web routing rule can be set by using the `aliyun.routing.port_$container_port` in the orchestration template or by updating the service configurations. What is the difference between these two methods?**

**A:** The two methods are essentially the same. The Web routing rule set by using the `aliyun.routing.port_$container_port` in the orchestration template can be reflected in the Web routing rule on the **Update Service** page. However, the Web routing rule set by updating the service configurations cannot be reflected in the orchestration template. Configuring the Web routing rule by **Update Service** facilitates you to operate in the console, troubleshoot the issues, and check the errors. This Web routing rule configuration form is converted into a label of the orchestration template and then used to update the service configurations.

**Q: What if the default routing service does not meet corner cases?**

**A：** A custom proxy image [registry.aliyuncs.com/acs/proxy](../../../../reseller.en-US/User Guide/Service discovery and load balancing/Custom routing - User guide.md#) can be a good solution. The image is based on HAProxy and supports parameter configurations that define HAProxy. This image also supports the dynamic service discovery, that is, the service is routed to a healthy container based on the service health status.

**Q: How to obtain the real IP address of the client after using the simple routing?**

**A:** For all the requests that use simple routing, Container Service adds x-forwarded-for information in the request headers.

``` {#codeblock_8u1_q8c_x8a}
x-forwarded-for: <Client IP address>
x-forwarded-for: <Proxy server IP>
```

**Note:** The header may contain multiple lines. The real IP address of the client can be obtained from the x-forwarded-for of the first line.

