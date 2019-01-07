# 使用yaml文件创建应用比对 {#concept_ikh_x5z_ggb .concept}

本文介绍容器服务Swarm集群与Kubernetes集群使用yaml文件创建应用时，Swarm集群下的yaml文件与Kubernetes集群下的yaml文件的对应关系。

## 背景信息 {#section_ubk_4zz_ggb .section}

在使用yaml文件创建应用时，Swarm集群与Kubernetes集群的yaml文件格式不一样：

-   您可以通过Kompose工具对Swarm集群的yaml文件进行自动转换。但转换后的内容仍需您核对检查。

    Kompose工具可以从Github上获取：[https://github.com/AliyunContainerService/kompose](https://github.com/AliyunContainerService/kompose)。

    **说明：** 目前Kompose工具对阿里云的一些定制标签还不支持，阿里云容器服务团队会持续研发逐步覆盖各个标签的支持。

-   您也可以手动改写yaml文件。

本文将基于容器服务Swarm的yaml文件，介绍介绍Kubernetes的yaml文件如何与之对应。文章中的yaml示例，仅作为示例，具体部署请依据具体情况添加及修改相关内容。

## 容器服务Swarm与Kubernetes集群的yaml文件比对 {#section_nrg_hb1_hgb .section}

**容器服务Swarm集群**

容器服务Swarm集群的yaml文件wordpress-swarm.yaml如下，注释中的阿拉伯数字与容器服务Kubernetes集群的yaml文件的注释对应。

```
web:		#---1
  image: registry.aliyuncs.com/acs-sample/wordpress:4.5		#---2
  ports:		#---3
    - '80'
  environment:		#---4
    WORDPRESS_AUTH_KEY: changeme			#---5
    WORDPRESS_SECURE_AUTH_KEY: changeme		#---5
    WORDPRESS_LOGGED_IN_KEY: changeme		#---5
    WORDPRESS_NONCE_KEY: changeme			#---5
    WORDPRESS_AUTH_SALT: changeme			#---5
    WORDPRESS_SECURE_AUTH_SALT: changeme		#---5
    WORDPRESS_LOGGED_IN_SALT: changeme		#---5
    WORDPRESS_NONCE_SALT: changeme			#---5
    WORDPRESS_NONCE_AA: changeme			#---5
  restart: always		#---6
  links:			#---7
    - 'db:mysql'
  labels:			#---8
    aliyun.logs: /var/log		#---9
    aliyun.probe.url: http://container/license.txt		#---10
    aliyun.probe.initial_delay_seconds: '10'			#---10
    aliyun.routing.port_80: http://wordpress			#---11
    aliyun.scale: '3'							#---12
db: 			#---1
  image: registry.aliyuncs.com/acs-sample/mysql:5.7		#---2
  environment:		#---4
    MYSQL_ROOT_PASSWORD: password		#---5
  restart: always		#---6
  labels:		#---8
    aliyun.logs: /var/log/mysql		#---9
```

**容器服务Kubernetes集群**

通过容器服务Swarm集群的wordpress-swarm.yaml文件部署的wordpress应用，在容器服务Kubernetes集群中对应2个服务：web和db。

在容器服务Kubernetes集群上需要2个部署（deployment）和2个服务（service）。2个Deployment创建2个Service，2个服务分别暴露2个应用的访问方式。

容器服务Swarm集群中的web应用对应Kubernetes集群的deployment和service如下：

**说明：** 以下yaml文件的内容仅作为示例说明与容器服务Swarm集群wordpress-swarm.yaml的对应关系，不可用作实际部署。

-   wordpress-kubernetes-web-deployment.yaml内容如下：

    ```
    apiVersion: apps/v1     # api版本
    kind: Deployment		# 创建资源的类型
    metadata:
      name: wordpress      #---1 
      labels:			#---8 在这里的label只能做标识作用
        app: wordpress
    spec:	#资源创建详细内容
      replicas: 2        #---12 设定实例（副本）个数
      selector:  
        matchLabels:
          app: wordpress
          tier: frontend
    strategy:
    type: Recreate
      template:   #模板定义POD的详细信息
        metadata:
          labels:  #与前面保持一致
            app: wordpress
            tier: frontend
        spec:    #定义pod中container的详细信息
          containers:    #
          - image: wordpress:4   #---2  对应于镜像及版本
            name: wordpress
            env:    #---4 环境变量设置，kubernetes上configmap，secret都可以通过env的方式使用
            - name: WORDPRESS_DB_HOST
              value: wordpress-mysql  #---7 通过名称指向需要访问的mysql，该名称与mysql service的名称相对应。
            - name: WORDPRESS_DB_PASSWORD    #---5 密码在这里使用，但kubernetes提供了secret进行密码封装
              valueFrom:
                secretKeyRef:
                  name: mysql-pass
                  key: password-wordpress
            ports:   #---3 容器内应用暴露的port
            - containerPort: 80
              name: wordpress
    livenessProbe:        #add health check    ---10 健康检查
              httpGet:
                path: /
                port: 8080
              initialDelaySeconds: 30
              timeoutSeconds: 5
              periodSeconds: 5
            readinessProbe:       #add health check	   ---10 健康检查
              httpGet:
                path: /
                port: 8080
              initialDelaySeconds: 5
              timeoutSeconds: 1
              periodSeconds: 5
            volumeMounts:  #使用存储卷，将存储卷真正挂到容器内部
            - name: wordpress-pvc
              mountPath: /var/www/html
          volumes:   #获取存储卷，需先进行PV和PVC的创建
          - name: wordpress-pvc
            persistentVolumeClaim:
              claimName: wordpress-pv-claim
    ```

-   wordpress-kubernetes-web-service.yaml内容如下：

    ```
    apiVersion: v1   #版本号
    kind: Service    #创建资源类型，在这里为service
    metadata:
      name: wordpress
      labels:
        app: wordpress
    spec:
      ports:
        - port: 80   #服务端口号
      selector:  #通过label进行应用的关联
        app: wordpress
        tier: frontend
      type: LoadBalancer  #---11 定义访问方式，此处为LoadBalancer类型的service，会自动创建SLB
    ```


容器服务Swarm集群中的db应用对应Kubernetes集群的deployment和service如下：

**说明：** 以下yaml文件的内容仅作为示例说明与容器服务Swarm集群wordpress-swarm.yaml的对应关系，不可用作实际部署。

-   wordpress-kubernetes-db-deployment.yaml内容如下：

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: wordpress-mysql
      labels:
        app: wordpress
    spec:
      selector:
        matchLabels:
          app: wordpress
          tier: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: wordpress
            tier: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-pass
                  key: password-mysql
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: wordpress-mysql-pvc
              mountPath: /var/lib/mysql
          volumes:
          - name: wordpress-mysql-pvc
            persistentVolumeClaim:
              claimName: wordpress-mysql-pv-claim
    ```

-   wordpress-kubernetes-db-service.yaml内容如下：

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: wordpress-mysql
      labels:
        app: wordpress
    spec:
      ports:
        - port: 3306
      selector:
        app: wordpress
        tier: mysql
          clusterIP: None
    ```


