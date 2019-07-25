# Custom routing - simple sample {#concept_m5l_t3z_xdb .concept}

In this example, an acs/proxy container is deployed,[EN-US\_TP\_7111.md\#section\_it1\_v2z\_xdb](reseller.en-US/User Guide/Service discovery and load balancing/Custom routing - User guide.md#section_it1_v2z_xdb) services are exposed by using a Server Load Balancer instance \(with the lb label\) externally, and an Nginx server is attached at the backend.[EN-US\_TP\_7033.md\#](reseller.en-US/User Guide/Service orchestrations/lb.md#) This example only shows the Nginx homepage, and other functions will be added based on the basic example.

**Note:** Different services cannot share the same Server Load Balancer. Otherwise, the backend machines of Server Load Balancer will be deleted and the services will become unavailable.

## Basic example {#section_wnf_v3z_xdb .section}

The compose template is as follows:

``` {#codeblock_toa_j7p_n4o}
lb:
    image: registry.aliyuncs.com/acs/proxy:0.5
    ports:
            - '80:80'
    restart: always
    labels:
         # Addon allows the proxy image to function as a subscription registry center and dynamically load the service route.
        aliyun.custom_addon: "proxy"
         # A proxy image container is deployed on each virtual machine (VM).
        aliyun.global: "true"
        # A Server Load Balancer instance is bound to the frontend.
        aliyun.lb.port_80: tcp://proxy_test:80
    environment:
         # Indicates the range of backend containers that support route loading. "*" indicates the whole cluster. By default, it indicates the services in applications.
        ADDITIONAL_SERVICES: "*"
appone:
    expose: # For proxied services, use expose or ports to tell proxy containers which port is to be exposed.
        - 80/tcp 
    image: 'nginx:latest'
    labels:
          # http/https/ws/wss are supported. Use your own domain name instead of the test domain name provided by Container Service.
        aliyun.proxy.VIRTUAL_HOST: "http://appone.example.com"
    restart: always
```

After the service is successfully started, the following figure appears.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7112/15640399595597_en-US.png)

**Enable session persistence**

``` {#codeblock_ikb_gi5_qx6}
lb:
    image: registry.aliyuncs.com/acs/proxy:0.5
    ports:
            - '80:80'
    restart: always
    labels:
         # Addon allows the proxy image to function as a subscription registry center and dynamically load the service route.
        aliyun.custom_addon: "proxy"
         # A proxy image container is deployed on each VM.
        aliyun.global: "true"
         # A Server Load Balancer instance is bound to the frontend.
        aliyun.lb.port_80: tcp://proxy_test:80
    environment:
         # Indicates the range of backend containers that support route loading. "*" indicates the whole cluster. By default, it indicates the services in applications.
        ADDITIONAL_SERVICES: "*"
appone:
    ports:
        - 80/tcp
        - 443/tcp
    image: 'nginx:latest'
    labels:
         # http/https/ws/wss are supported.
        aliyun.proxy.VIRTUAL_HOST: "http://appone.example.com"
         # Session persistence is enabled, the cookie method is applied, and the key is CONTAINERID.
        aliyun.proxy.COOKIE: "CONTAINERID insert indirect"
    restart: always
```

## Customize 503 page {#section_n4f_v3z_xdb .section}

When the VIP address of the Server Load Balancer instance instead of the domain name is entered, the 503 error page is returned as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7112/15640399595598_en-US.png)

To add messages to the 503 page, add the `/errors` folder to the VM where the container resides and add the `/errors/503.http` file with the following content:

``` {#codeblock_m9h_t4c_0lf}
HTTP/1.0 503 Service Unavailable
Cache-Control: no-cache
Connection: close
Content-Type: text/html;charset=UTF-8
<html><body><h1>503 Service Unavailable</h1>
<h3>No server is available to handle this request.</h3>
If this page is returned, a problem occurs during the service access process. Follow these steps for troubleshooting:
<li>If you are the visitor of this application, contact the application maintainer to solve the problem. </li>
<li>If you are the application maintainer, view the following information. </li>
<li>You are using the simple routing service. The request is sent from Server Load Balancer to the acsrouting application container then to your application container. Follow these steps for troubleshooting. </li>
<li>Log on to the Container Service console. Click "Services" in the left-side navigation pane. Select the corresponding cluster on the "Service List" page. Click the name of the service exposed to the public network. View the "Access Endpoint" of the service, and check whether your access domain name is the same as the domain name configured in the corresponding service. </li>
<li>Locate and troubleshoot the problem according to the Simple routing - service link troubleshooting. </li>
<li>See Routing FAQs. </li>
<li>If the problem persists, open a ticket and contact the technical staff for help. We will serve you faithfully.</li>
</body></html>
```

You can modify the error page as per your needs. The compose template is modified as follows:

``` {#codeblock_jcp_n4v_13v}
lb:
    image: registry.aliyuncs.com/acs/proxy:0.5
    ports:
            - '80:80'
    restart: always
    labels:
        # Addon allows the proxy image to function as a subscription registry center and dynamically load the service route.
        aliyun.custom_addon: "proxy"
         # A proxy image container is deployed on each VM.
        aliyun.global: "true"
         # A Server Load Balancer instance is bound to the frontend.
        aliyun.lb.port_80: tcp://proxy_test:80
    environment:
         # Indicates the range of backend containers that support route loading. "*" indicates the whole cluster. By default, it indicates the services in applications.
        ADDITIONAL_SERVICES: "*"
        EXTRA_FRONTEND_SETTINGS_80: "errorfile 503 /usr/local/etc/haproxy/errors/503.http"
    volumes:
        - /errors/:/usr/local/etc/haproxy/errors/
appone:
    ports:
        - 80/tcp
        - 443/tcp
    image: 'nginx:latest'
    labels:
          # You can specify paths when configuring URLs. In this example, http/https/ws/wss are supported.
        aliyun.proxy.VIRTUAL_HOST: "http://appone.example.com"
    restart: always
```

After entering the VIP address of the Server Load Balancer instance, the 503 page is displayed as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7112/15640399595599_en-US.png)

## Support extensive domain names {#section_fpf_v3z_xdb .section}

Modify the configurations as follows to enable the backend of Nginx to support extensive domain names \(that is, the Nginx homepage can be accessed by using `appone.example.com` and `*.example.com`\).

``` {#codeblock_xmr_pq2_btp}
lb:
    image: registry.aliyuncs.com/acs/proxy:0.5
    ports:
            - '80:80'
    restart: always
    labels:
        # Addon allows the proxy image to function as a subscription registry center and dynamically load the service route.
        aliyun.custom_addon: "proxy"
         # A proxy image container is deployed on each VM.
        aliyun.global: "true"
         # A Server Load Balancer instance is bound to the frontend.
        aliyun.lb.port_80: tcp://proxy_test:80
    environment:
         # Indicates the range of backend containers that support route loading. "*" indicates the whole cluster. By default, it indicates the services in applications.
        ADDITIONAL_SERVICES: "*"
        EXTRA_FRONTEND_SETTINGS_80: "errorfile 503 /usr/local/etc/haproxy/errors/503.http"
    volumes:
        - /errors/:/usr/local/etc/haproxy/errors/
appone:
    ports:
        - 80/tcp
        - 443/tcp
    image: 'nginx:latest'
    labels:
        # You can specify paths when configuring URLs. In this example, http/https/ws/wss are supported.
        aliyun.proxy.VIRTUAL_HOST: "http://*.example.com"
    restart: always
```

Bind a host and enter the domain name `www.example.com`. The Nginx homepage is displayed as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7112/15640399605600_en-US.png)

## Configure default backend {#section_ppf_v3z_xdb .section}

Remove the URL configuration and modify the configurations as follows to enable access to Nginx at the backend by using an IP address.

``` {#codeblock_i1x_swa_iqr}
lb:
    image: registry.aliyuncs.com/acs/proxy:0.5
    ports:
            - '80:80'
    restart: always
    labels:
         # Addon allows the proxy image to function as a subscription registry center and dynamically load the service route.
        aliyun.custom_addon: "proxy"
         # A proxy image container is deployed on each VM.
        aliyun.global: "true"
         # A Server Load Balancer instance is bound to the frontend.
        aliyun.lb.port_80: tcp://proxy_test:80
    environment:
         # Indicates the range of backend containers that support route loading. "*" indicates the whole cluster. By default, it indicates the services in applications.
        ADDITIONAL_SERVICES: "*"
        # Specify the error page when 503 is returned.
        EXTRA_FRONTEND_SETTINGS_80: "errorfile 503 /usr/local/etc/haproxy/errors/503.http"
    volumes:
        # Mount the error page to the container from the host.
        - /errors/:/usr/local/etc/haproxy/errors/
appone:
    ports:
        - 80/tcp
        - 443/tcp
    image: 'nginx:latest'
    labels:
         # Indicates that the service must be proxied.
        aliyun.proxy.required: "true"
    restart: always
```

After entering the VIP address of the Server Load Balancer instance, the Nginx homepage is displayed as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7112/15640399605601_en-US.png)

## Select backend based on URL parameter values {#section_aqf_v3z_xdb .section}

You can use different backend proxies based on different URL parameter values.

The following example shows how to access the appone service, that is, the Nginx homepage, by using `http://www.example.com?backend=appone` and how to access the apptwo service, that is, the hello world homepage, by using `http://www.example.com?backend=apptwo` . The application template codes are as follows:

``` {#codeblock_c1p_xlt_7in}
lb:
    image: registry.aliyuncs.com/acs/proxy:0.5
    ports:
            - '80:80'
    restart: always
    labels:
         # Addon allows the proxy image to function as a subscription registry center and dynamically load the service route.
        aliyun.custom_addon: "proxy"
         # A proxy image container is deployed on each VM.
        aliyun.global: "true"
        # A Server Load Balancer instance is bound to the frontend.
        aliyun.lb.port_80: tcp://proxy_test:80
    environment:
         # Indicates the range of backend containers that support route loading. "*" indicates the whole cluster. By default, it indicates the services in applications.
        ADDITIONAL_SERVICES: "*"
         # Obtain the value of the "backend" parameter in the URL and modify the HOST header to the backend domain name which needs to be matched.
        EXTRA_FRONTEND_SETTINGS_80: " http-request set-header HOST %[urlp(backend)].example.com"
appone:
    ports:
        - 80/tcp
        - 443/tcp
    image: 'nginx:latest'
    labels:
         # You can specify paths when configuring URLs. In this example, http/https/ws/wss are supported.
        aliyun.proxy.VIRTUAL_HOST: "http://appone.example.com"
    restart: always
apptwo:
    ports:
        - 80/tcp
    image: 'registry.cn-hangzhou.aliyuncs.com/linhuatest/hello-world:latest'
    labels:
         # You can specify paths when configuring URLs. In this example, http/https/ws/wss are supported.
        aliyun.proxy.VIRTUAL_HOST: "http://apptwo.example.com"
    restart: always
```

Bind a host and enter the link `http://www.example.com?backend=appone`. Then, the Nginx homepage for the appone service is displayed as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7112/15640399605602_en-US.png)

Bind a host and enter the link `http://www.example.com?backend=apptwo`. Then, the hello world homepage for the apptwo service is displayed as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7112/15640399605603_en-US.png)

## Record access logs {#section_nqf_v3z_xdb .section}

``` {#codeblock_ojl_vog_krc}
lb:
    image: registry.aliyuncs.com/acs/proxy:0.5
    ports:
            - '80:80'
    restart: always
    labels:
        # Addon allows the proxy image to function as a subscription registry center and dynamically load the service route.
        aliyun.custom_addon: "proxy"
         # A proxy image container is deployed on each VM.
        aliyun.global: "true"
          # A Server Load Balancer instance is bound to the frontend.
        aliyun.lb.port_80: tcp://proxy_test:80
    environment:
         # Indicates the range of backend containers that support route loading. "*" indicates the whole cluster. By default, it indicates the services in applications.
        ADDITIONAL_SERVICES: "*"
        EXTRA_DEFAULT_SETTINGS: "log rsyslog local0,log global,option httplog"
    links:
        - rsyslog:rsyslog
rsyslog:
    image: registry.cn-hangzhou.aliyuncs.com/linhuatest/rsyslog:latest
appone:
    ports:
        - 80/tcp
        - 443/tcp
    image: 'nginx:latest'
    labels:
        # http/https/ws/wss are supported.
        aliyun.proxy.VIRTUAL_HOST: "http://appone.example.com"
    restart: always
```

Logs are printed directly to the standard output of the rsyslog container. The access logs of custom routing can be viewed by using `docker logs $rsyslog_container_name`.

## Server Load Balancer between services {#section_wqf_v3z_xdb .section}

The following template creates a Server Load Balancer service `lb` and an application service `appone` to provide services externally with the domain name `appone.example.com` .

``` {#codeblock_xw6_2an_hau}
lb:
    image: registry.aliyuncs.com/acs/proxy:0.5
    Hostname: proxy # Specify the domain name of the service as proxy, which is resolved to all containers with this image deployed.
    ports:
            - '80:80'
    restart: always
    labels:
         # Addon allows the proxy image to function as a subscription registry center and dynamically load the service route.
        aliyun.custom_addon: "proxy"
        # A proxy image container is deployed on each VM.
        aliyun.global: "true"
        # A Server Load Balancer instance is bound to the frontend.
        aliyun.lb.port_80: tcp://proxy_test:80
    environment:
        # Indicates the range of backend containers that support route loading. "*" indicates the whole cluster. By default, it indicates the services in applications.
        ADDITIONAL_SERVICES: "*"
appone:
    ports:
        - 80/tcp
        - 443/tcp
    image: 'nginx:latest'
    labels:
         # http/https/ws/wss are supported.
        aliyun.proxy.VIRTUAL_HOST: "http://appone.example.com"
    restart: always
```

The following template is used as a client to access the `appone` application service, but the access path is used to request access to the Server Load Balancer service `lb` and then provide a reverse proxy for the appone application service.

``` {#codeblock_82j_8ye_ub0}
restclient: # Simulate rest service consumers.
  image: registry.aliyuncs.com/acs-sample/alpine:3.3
  command: "sh -c 'apk update; apk add curl; while true; do curl --head http://appone.example.com; sleep 1; done'" # Access the rest service and test Server Load Balancer.
  tty: true  
  external_links: 
    - "proxy:appone.example.com" # Specify the domain name of the link service and the alias of the domain name.
```

In the containers of the `restclient` service, the `appone.example.com` domain name is resolved to the IP addresses of all containers of the Server Load Balancer service `lb`.

``` {#codeblock_goo_5nv_t20}
/ # drill appone.example.com
;; ->>HEADER<<- opcode: QUERY, rcode: NOERROR, id: 60917
;; flags: qr rd ra ; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 0
;; QUESTION SECTION:
;; appone.example.com.  IN A
;; ANSWER SECTION:
appone.example.com.     600 IN A 172.18.3.4
appone.example.com.     600 IN A 172.18.2.5
appone.example.com.     600 IN A 172.18.1.5
;; AUTHORITY SECTION:
;; ADDITIONAL SECTION:
;; Query time: 0 msec
;; SERVER: 127.0.0.11
;; WHEN: Mon Sep 26 07:09:40 2016
;; MSG SIZE rcvd: 138
```

## Configure monitoring page {#section_nrf_v3z_xdb .section}

``` {#codeblock_8xn_tqs_56m}
lb:
    image: registry.aliyuncs.com/acs/proxy:0.5
    ports:
            - '80:80'
            -127. 0.5.1: 1935: 1935 '# The port that monitoring page exposes to the public network. Configure the port with due care because of the potential security risk.
    restart: always
    labels:
        aliyun.custom_addon: "proxy"
        aliyun.global: "true"
        aliyun.lb.port_80: tcp://proxy_test:80
    environment:
        ADDITIONAL_SERVICES: "*"
        STATS_AUTH: "admin:admin" # The logon account and password used for monitoring, which are customizable.
        STATS_PORT: "1935" # The port used for monitoring, which is customizable.
appone:
    expose: 
        - 80/tcp 
    image: 'nginx:latest'
    labels:
        aliyun.proxy.VIRTUAL_HOST: "http://appone.example.com"
    restart: always
```

Log on to each machine where the custom routing image resides \(each machine can receive the request, no matter the application container is on which machine\) and request the `acs/proxy` health check page.

**Note:** Configure the correct username and password according to the environment variable STATS\_AUTH of the application template.

``` {#codeblock_fgm_tom_9ox}
root@c68a460635b8c405e83c052b7c2057c7b-node2:~# curl -Ss -u admin:admin 'http://127.0.0.1:1935/' &> test.html
```

Copy the page `test.html` to a machine with browsers and open the local file `test.html` with the browser. View the stats monitoring statistics page. Green indicates the network from container `acs/proxy` to backend containers is connected and the container acs/proxy is working normally. Other colors indicate an exception.

