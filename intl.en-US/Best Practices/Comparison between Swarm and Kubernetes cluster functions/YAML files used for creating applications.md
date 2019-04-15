# YAML files used for creating applications {#concept_ikh_x5z_ggb .concept}

This topic describes the relation between the YAML files used in a Swarm cluster and those used in Kubernetes cluster for creating applications.

## Background {#section_ubk_4zz_ggb .section}

The formats of the YAML files used to create applications in a Swarm cluster and a Kubernetes cluster are different.

-   You can use Kompose to convert a Swarm cluster YAML file to a Kubernetes cluster YAML. But you still need to check the converted YAML file.

    To obtain Kompose, see [https://github.com/AliyunContainerService/kompose](https://github.com/AliyunContainerService/kompose).

    You can download Kompose at one of the following URLs:

    -   The Kompose download URL for the Mac operating system is [http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/swarm/kompose-darwin-amd64](http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/swarm/kompose-darwin-amd64)
    -   The Kompose download URL for the Linux operating system is [http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/swarm/kompose-linux-amd64](http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/swarm/kompose-linux-amd64)
    -   The Kompose download URL for the Windows operating system is [http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/swarm/kompose-windows-amd64.exe](http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/swarm/kompose-windows-amd64.exe)
    **Note:** Kompose does not support certain customized labels in Alibaba Cloud. The Alibaba Cloud Container Service Team is developing solutions so that Kompose can support all customized labels.

    |Tag|Related link|
    |---|------------|
    |external|[External ](../../../../../reseller.en-US/User Guide/Service orchestrations/External .md#)|
    |dns\_options|[dns\_options](../../../../../reseller.en-US/User Guide/Service orchestrations/dns_options.md#)|
    |oom\_kill\_disable|[oom\_kill\_disable](../../../../../reseller.en-US/User Guide/Service orchestrations/oom_kill_disable.md#)|
    |affinity:service|[Service deployment constraints \(affinity:service\)](../../../../../reseller.en-US/User Guide/Service orchestrations/Service deployment constraints (affinity:service).md#)|

-   You can also manually modify a Swarm cluster YAML file to make it compatible with a Kubernetes cluster.

This topic describes the relation between the YAML files used in the two types of cluster. You must orchestrate YAML files according to conditions required by the application deployment. The YAML files in this topic are used only as examples.

## Comparison between YAML files used in a Swarm and those used in a Kubernetes cluster for creating applications {#section_nrg_hb1_hgb .section}

**Container Service Swarm cluster**

The following is a wordpress-swarm.yaml file used in the Swarm cluster. Note each parameter marked by a number in the following YAML file corresponds to the parameter marked by the same number in the YAML file used in the Kubernetes cluster.

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
    aliyun.logs: /var/log/mysql
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

**Container Service Kubernetes cluster**

The WordPress application deployed through the wordpress-swarm.yaml file in the Swarm cluster corresponds to two services in the Kubernetes cluster, that is, the Web service and the db service.

A Kubernetes cluster requires two deployments and two services. You must create one service for each deployment. The two services are used to expose the access methods for the two applications.

In the Kubernetes cluster, the deployment and the service that correspond to the Web application of the Swarm cluster are created by using the following YAML files:

**Note:** The following YAML files are used only as examples to describe their relation with the wordpress-swarm.yaml file. We recommend that you do not use these files to deploy your applications.

-   wordpress-kubernetes-web-deployment.yaml file

    ```
    apiVersion: apps/v1     # API version
    kind: Deployment		# type of the resource that you want to create
    metadata:
      name: wordpress      #---1 
      labels:			#---8 This label is only used to mark the resource.
        app: wordpress
    spec:	#resource details
      replicas: 2        #---12 Indicates the number of replicas.
      selector:  
        matchLabels:
          app: wordpress
          tier: frontend
    strategy:
    type: Recreate
      template:   #Defines the pod details.
        metadata:
          labels:  #Keeps settings consistent with the preceding labels parameter.
            app: wordpress
            tier: frontend
        spec:    #Defines the container details in the pod.
          containers:    #
          - image: wordpress:4   #---2  Corresponds to the image name and version.
            name: wordpress
            env:    #---4 Indicates environment variable settings, including config maps and secrets in Kubernetes.
            - name: WORDPRESS_DB_HOST
              value: wordpress-mysql  #---7 Indicates the MySQL that you want to access.
            - name: WORDPRESS_DB_PASSWORD    #---5 Indicates a password. Note Kubernetes provides a secret to encrypt the password.
              valueFrom:
                secretKeyRef:
                  name: mysql-pass
                  key: password-wordpress
            ports:   #---3 Indicates the exposed port of the application within the container.
            - containerPort: 80
              name: wordpress
    livenessProbe:        #Add a health check setting   ---10 health check
              httpGet:
                path: /
                port: 8080
              initialDelaySeconds: 30
              timeoutSeconds: 5
              periodSeconds: 5
            readinessProbe:       #Add a health check setting  ---10 health check
              httpGet:
                path: /
                port: 8080
              initialDelaySeconds: 5
              timeoutSeconds: 1
              periodSeconds: 5
            volumeMounts:  #Mount the volume to the container.
            - name: wordpress-pvc
              mountPath: /var/www/html
          volumes:   #Indicates to obtain the volume. You need to first create a PV and a PVC.
          - name: wordpress-pvc
            persistentVolumeClaim:
              claimName: wordpress-pv-claim
    ```

-   wordpress-kubernetes-web-service.yaml file

    ```
    apiVersion: v1   #version number
    kind: Service    #Indicates the type of the resource that you want to create. It is Service in this YAML file.
    metadata:
      name: wordpress
      labels:
        app: wordpress
    spec:
      ports:
        - port: 80   #service port
      selector:  #Indicates to associate the service with the application through the label.
        app: wordpress
        tier: frontend
      type: LoadBalancer  #---11 Defines the access method. This YAML file specifies an SLB service and an SLB instance will be created automatically.
    ```


In the Kubernetes cluster, the deployment and the service that correspond to the Web application of the Swarm cluster are created by using the following YAML files:

**Note:** The following YAML files are only used as examples to describe their relation with the wordpress-swarm.yaml file. We recommend that you do not use these files for application deployment.

-   wordpress-kubernetes-db-deployment.yaml file

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

-   wordpress-kubernetes-db-service.yaml file

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


