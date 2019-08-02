# Access blockchain network by using applications {#task_u3r_gqz_vdb .task}

-   The blockchain network is configured and deployed in a Kubernetes cluster.
-   The Internet IP address and external port are configured if the application is deployed outside container clusters.

After creating the blockchain network by using the Alibaba Cloud Container Service blockchain solution, you can access services on the blockchain network by using blockchain applications based on Hyperledger Fabric SDKs. The blockchain solution supports the Connection Profile function from the 1.1 Hyperledger Fabric version.

-   The blockchain application can be deployed on the Alibaba Cloud container cluster together with the blockchain network. In this mode, the application directly accesses services by using the name and port of each blockchain service.
-   The blockchain application can also be deployed outside Alibaba Cloud container clusters. In this mode, the application accesses services by using the external address of the blockchain network and the external port of each service.

In this example, deploy the blockchain application outside Alibaba Cloud container clusters. The provided application is a balance transfer application developed and adapted based on Hyperledger Fabric.

You can use the application provided in this example, the official Hyperledger Fabric example application \(for example, [fabric-samples](https://github.com/hyperledger/fabric-samples)\), or self-developed blockchain application. Adapt the application by referring to the source codes of the application provided in the example to access the Alibaba Cloud Container Service blockchain network.

Adapt the existing blockchain application as follows:

-   Directly use the script download-from-fabric-network.sh that is provided in the sample code to automatically download blockchain network configurations from the newly deployed blockchain network with one click, including certificates, keys, and blockchain network configuration files \(basically the config.json and network-config.yaml\).
-   Make sure that the blockchain application can be correctly loaded to the preceding blockchain network configurations.
-   If the blockchain application directly uses the channel name, external address, node name, or domain name of the blockchain, replace them with parameters in the configuration files \(config.json and network-config.yaml\) to keep the configurations of the application consistent with those of the target blockchain network.

1.   Download the source codes of the blockchain application provided in the example to the local development environment. The command is as follows: 

    ```
    git clone https://github.com/AliyunContainerService/solution-blockchain-demo.git
    ```

2.   Perform subsequent operations based on the README document of the blockchain application provided in the example. 

    Chinese version: [https://github.com/AliyunContainerService/solution-blockchain-demo/blob/master/balance-transfer-app/README.cn.md](https://github.com/AliyunContainerService/solution-blockchain-demo/blob/master/balance-transfer-app/README.cn.md)

    English version: [https://github.com/AliyunContainerService/solution-blockchain-demo/blob/master/balance-transfer-app/README.md](https://github.com/AliyunContainerService/solution-blockchain-demo/blob/master/balance-transfer-app/README.md)

    **Note:** For the same blockchain network, CLI sample and application sample cannot run at the same time. Select to run only one type of sample for each blockchain network.


