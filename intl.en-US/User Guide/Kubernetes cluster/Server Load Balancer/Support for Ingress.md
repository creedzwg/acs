# Support for Ingress {#concept_zny_hrs_vdb .concept}

In Kubernetes clusters, Ingress is a collection of rules that authorize inbound connection to the cluster services and provides you with Layer-7 Server Load Balancer capabilities. You can provide the Ingress configuration with externally accessible URL, Server Load Balancer, SSL, and name-based virtual host.

## Prerequisites {#section_bnd_jrs_vdb .section}

To test the complex routing service, create an Nginx application in this example. You must create the Nginx deployment and multiple services in advance to observe the routing effect. Replace with your own service in the actual test.In the actual test enter your own service.

```
root@master # kubectl run nginx --image=registry.cn-hangzhou.aliyuncs.com/acs/netdia:latest

root@master # kubectl expose deploy nginx --name=http-svc --port=80 --target-port=80
root@master # kubectl expose deploy nginx --name=http-svc1 --port=80 --target-port=80
root@master # kubectl expose deploy nginx --name=http-svc2 --port=80 --target-port=80
root@master # kubectl expose deploy nginx --name=http-svc3 --port=80 --target-port=80
```

## Simple routing service {#section_dnd_jrs_vdb .section}

Create a simple Ingress service by using the following commands. All the accesses to the /svc path are routed to the Nginx service. `nginx.ingress.kubernetes.io/rewrite-target: /` redirects the path /svcto the path / that can be recognized by backend services.

```
root@master # cat <<EOF | kubectl create -f -
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: simple
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /svc
        backend:
          serviceName: http-svc
          servicePort: 80
EOF
root@master # kubectl get ing
NAME            HOSTS         ADDRESS          PORTS     AGE
simple          *             101.37.192.211   80        11s
```

Now visit `http://101.37.192.211/svc` to access the Nginx service.

## Simple fanout routing based on domain names {#section_lnd_jrs_vdb .section}

If you have multiple domain names providing different external services, you can generate the following configuration to implement a simple fanout effect based on domain names:

```
root@master # cat <<EOF | kubectl create -f - 
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: simple-fanout
spec:
  rules:
  - host: foo.bar.com
    http:
      paths:
      - path: /foo
        backend:
          serviceName: http-svc1
          servicePort: 80
      - path: /bar
        backend:
          serviceName: http-svc2
          servicePort: 80
  - host: foo.example.com
    http:
      paths:
      - path: /film
        backend:
          serviceName: http-svc3
          servicePort: 80    
EOF
root@master # kubectl get ing
NAME            HOSTS         ADDRESS          PORTS     AGE
simple-fanout   *             101.37.192.211   80        11s
```

Then, you can access the `http-svc1` service by using `http://foo.bar.com/foo`, access the `http-svc2` service by using `http://foo.bar.com/bar`, and access the `http-svc3` service by using`http://foo.example.com/film`.

**Note:** 

-   In a production environment, point the domain name to the preceding returned address `101.37.192.211`.
-   In a testing environment, you can modify the `hosts` file to add a domain name mapping rule.

    ```
    101.37.192.211 foo.bar.com
    101.37.192.211 foo.example.com
    ```


## Default domain name of simple routing {#section_znd_jrs_vdb .section}

It does not matter if you do not have the domain name address. Container Service binds a default domain name for Ingress service. You can use this default domain name to access the services. The domain name is in the format of `*.[cluster-id].[region-id].alicontainer.com`. You can obtain the address on the cluster Basic Information page in the console.

Use the following configuration to expose two services with the default domain name.

```
root@master # cat <<EOF | kubectl create -f - 
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: shared-dns
spec:
  rules:
  - host: foo.[cluster-id].[region-id].alicontainer.com ##Replace with the default service access domain name of your cluster.
    http:
      paths:
      - path: /
        backend:
          serviceName: http-svc1
          servicePort: 80
  - host: bar.[cluster-id].[region-id].alicontainer.com ##Replace with the default service access domain name of your cluster.
    http:
      paths:
      - path: /
        backend:
          serviceName: http-svc2
          servicePort: 80    
EOF
root@master # kubectl get ing
NAME            HOSTS         ADDRESS          PORTS     AGE
shared-dns   foo.[cluster-id].[region-id].alicontainer.com,bar.[cluster-id].[region-id].alicontainer.com             47.95.160.171   80        40m
```

Then, you can access the `http-svc1` service by using `http://foo.[cluster-id].[region-id].alicontainer.com/`and access the `http-svc2` service by using `http://bar.[cluster-id].[region-id].alicontainer.com`.

## Configure a safe routing service {#section_j4d_jrs_vdb .section}

Management of multiple certificates is supported to provide security protection for your services.

1.  Prepare your service certificate.

    If no certificate is available, generate a test certificate in the following method:

    **Note:** The domain name must be consistent with your Ingress configuration.

    ```
    root@master # openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
    ```

    The above command generates a certificate file tls.crt and a private key file tls.key.

    Create a Kubernetes secret named foo.bar using the certificate and private key. The secret must be referenced when you create the Ingress.

    ```
    root@master # kubectl create secret tls foo.bar --key tls.key --cert tls.crt
    ```

2.  Create a safe Ingress service.

    ```
    root@master # cat <<EOF | kubectl create -f - 
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: tls-fanout
    spec:
      tls:
      - hosts:
        - foo.bar.com
        secretName: foo.bar
      rules:
      - host: foo.bar.com
        http:
          paths:
          - path: /foo
            backend:
              serviceName: http-svc1
              servicePort: 80
          - path: /bar
            backend:
              serviceName: http-svc2
              servicePort: 80
    EOF
    root@master # kubectl get ing
    NAME            HOSTS         ADDRESS          PORTS     AGE
    tls-fanout      *             101.37.192.211   80        11s
    ```

3.  Follow the notes in **Simple fanout routing based on domain names** to configure the `hosts` file or set the domain name to access the TLS service.

    You can access the `http-svc1` service by using `http://foo.bar.com/foo` and access the `http-svc2` service by using `http://foo.bar.com/bar`.

    You can also access the HTTPS service by using HTTP. By default, Ingress redirects HTTP access configured with HTTPS to the HTTPS address. Therefore, access to `http://foo.bar.com/foo` will be automatically redirected to `https://foo.bar.com/foo`.


## Deploy Ingress in Kubernetes dashboard {#section_vvf_12y_wdb .section}

1.  Save the following yml code to the `nginx-ingress.yml`file.

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: simple
    spec:
      rules:
      - http:
          paths:
          - path: /svc
            backend:
              serviceName: http-svc
              servicePort: 80
    ```

2.  Log on to the [容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs). Under Kubernetes, click Clusters in the left-side navigation pane. Click **Dashboard**at the right of the cluster to enter the Kubernetes dashboard.
3.  Click **CREATE** in the upper-right corner to create an application.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6932/15500736654662_en-US.png)

4.  Click the **CREATE FROM FILE** tab. Select the `nginx-ingress.yml` file you saved.
5.  Click **UPLOAD**.

    Then an Ingress Layer-7 proxy route will be created to the `http-svc` service.

6.  Click default under Namespace in the left-side navigation pane. Click **Ingresses** in the left-side navigation pane.

    You can view the created Ingress resource and its access address `http://118.178.174.161/svc`.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6932/15500736654663_en-US.png)

7.  Enter the address in the browser to access the created `http-svc` service.

