# 通过负载均衡（Server Load Balancer）访问服务 {#concept_sxm_l3s_vdb .concept}

您可以使用阿里云负载均衡来访问服务。

**说明：** 如果您的集群的cloud-controller-manager版本大于等于v1.9.3，对于指定已有SLB的时候，系统默认不再为该SLB处理监听，用户需要手动配置该SLB的监听规则。

执行以下命令，可查看cloud-controller-manager的版本。

```
root@master # kubectl get po -n kube-system -o yaml|grep image:|grep cloud-con|uniq

  image: registry-vpc.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3
```

## 通过命令行操作 {#section_umc_p3s_vdb .section}

1.  通过命令行工具创建一个 Nginx 应用。

    ```
    root@master # kubectl run nginx --image=registry.aliyuncs.com/acs/netdia:latest
    root@master # kubectl get po 
    NAME                                   READY     STATUS    RESTARTS   AGE
    nginx-2721357637-dvwq3                 1/1       Running   1          6s
    ```

2.  为 Nginx 应用创建阿里云负载均衡服务，指定 `type=LoadBalancer` 来向外网用户暴露 Nginx 服务。

    ```
    root@master # kubectl expose deployment nginx --port=80 --target-port=80 --type=LoadBalancer
    root@master # kubectl get svc
    NAME                  CLUSTER-IP      EXTERNAL-IP      PORT(S)                        AGE
    nginx                 172.19.10.209   101.37.192.20   80:31891/TCP                   4s
    ```

3.  在浏览器中访问 `http://101.37.192.20`，来访问您的 Nginx 服务。

## 通过 Kubernetes Dashboard 操作 {#section_lb5_p3s_vdb .section}

1.  将下面的 yml code 保存到 `nginx-svc.yml`文件中。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: nginx
      name: http-svc
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

2.  登录[容器服务管理控制台](https://cs.console.aliyun.com)，单击目标集群右侧的**控制台**，进入 Kubernetes Dashboard 页面。
3.  单击**创建**，开始创建应用。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6930/15531564954623_zh-CN.png)

4.  单击**使用文件创建**。选择刚才保存的nginx-svc.yml 文件
5.  单击**上传**。

    这样会创建一个阿里云负载均衡实例指向创建的 Nginx 应用，服务的名称为 `http-svc`。

6.  在 Kubernetes Dashboard 上定位到 default 命名空间，选择服务。

    可以看到刚刚创建的 `http-svc` 的 Nginx 服务和机器的负载均衡地址 `http://114.55.79.24:80`。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6930/15531564954625_zh-CN.png) 

7.  将该地址拷贝到浏览器中即可访问该服务。

## 更多信息 {#section_a31_43s_vdb .section}

阿里云负载均衡还支持丰富的配置参数，包含健康检查、收费类型、负载均衡类型等参数。详细信息参见[负载均衡配置参数表](intl.zh-CN/用户指南/Kubernetes 集群/负载均衡/通过负载均衡（Server Load Balancer）访问服务.md#table_s31_43s_vdb)。

## 注释 {#section_b31_43s_vdb .section}

阿里云可以通过注释的形式支持丰富的负载均衡功能。

**使用已有的内网 SLB**

需要指定两个annotation。注意修改成您自己的 Loadbalancer-id。

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-address-type: intranet
    service.beta.kubernetes.io/alicloud-loadbalancer-id: your-loadbalancer-id
  labels:
    run: nginx
  name: nginx
  namespace: default
spec:
  ports:
  - name: web
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
  sessionAffinity: None
  type: LoadBalancer
```

然后保存为 slb.svc 后，执行 `kubectl apply -f slb.svc`。

**创建 HTTPS 类型的 Loadbalancer**

先在阿里云控制台上创建一个证书并记录 cert-id，然后使用如下 annotation 创建一个 HTTPS 类型的 SLB。

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-cert-id: your-cert-id
    service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port: "https:443"
  labels:
    run: nginx
  name: nginx
  namespace: default
spec:
  ports:
  - name: web
    port: 443
    protocol: TCP
    targetPort: 443
  selector:
    run: nginx
  sessionAffinity: None
  type: LoadBalancer
```

**说明：** 注释的内容是区分大小写的。

|注释|描述|默认值|
|--|--|---|
|service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port|多个值之间由逗号分隔，比如：https:443,http:80|无|
|service.beta.kubernetes.io/alicloud-loadbalancer-address-type|取值可以是 internet 或者 intranet|internet|
|service.beta.kubernetes.io/alicloud-loadbalancer-slb-network-type|负载均衡的网络类型，取值可以是 classic 或者 vpc|classic|
|service.beta.kubernetes.io/alicloud-loadbalancer-charge-type|取值可以是 paybytraffic 或者 paybybandwidth|paybybandwidth|
|service.beta.kubernetes.io/alicloud-loadbalancer-id|负载均衡实例的 ID。通过 loadbalancer-id 指定您已有的 SLB，已有 listener 会被覆盖， 删除 service 时该 SLB 不会被删除。|无|
|service.beta.kubernetes.io/alicloud-loadbalancer-backend-label|通过 label 指定 SLB 后端挂哪些节点。|无|
|service.beta.kubernetes.io/alicloud-loadbalancer-region|负载均衡所在的地域|无|
|service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth|负载均衡的带宽|50|
|service.beta.kubernetes.io/alicloud-loadbalancer-cert-id|阿里云上的证书 ID。您需要先上传证书|“”|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-flag|取值是 on 或者 off|默认为 off。TCP 不需要改参数。因为 TCP 默认打开健康检查，用户不可设置。|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-type|参见 [CreateLoadBalancerTCPListener](../../../../../intl.zh-CN/API参考/TCP监听/CreateLoadBalancerTCPListener.md#)| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-uri|参见 [HealthCheck](https://www.alibabacloud.com/help/zh/doc-detail/27594.htm)| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-port|参见 [HealthCheck](https://www.alibabacloud.com/help/zh/doc-detail/27594.htm)| |
|service.beta.kubernetes.io/alicloud-loadbalancer-healthy-threshold|参见 [HealthCheck](https://www.alibabacloud.com/help/zh/doc-detail/27594.htm)| |
|service.beta.kubernetes.io/alicloud-loadbalancer-unhealthy-threshold|参见 [HealthCheck](https://www.alibabacloud.com/help/zh/doc-detail/27594.htm)| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval|参见 [HealthCheck](https://www.alibabacloud.com/help/zh/doc-detail/27594.htm)| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-timeout|参见 [HealthCheck](https://www.alibabacloud.com/help/zh/doc-detail/27594.htm)| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-timeout|参见 [HealthCheck](https://www.alibabacloud.com/help/zh/doc-detail/27594.htm)| |

