# Network {#concept_hz3_ng1_hgb .concept}

This topic compares the networks used by Swarm clusters and Kubernetes clusters.

## Swarm cluster {#section_xdk_2l1_hgb .section}

A Swarm cluster can use either of the following two networks:

-   A VPC
-   A classic network

## Kubernetes cluster {#section_etn_1l1_hgb .section}

A Kubernetes cluster can only use a VPC. For more information, see [Plan Kubernetes CIDR blocks under VPC](../../../../../reseller.en-US/Best Practices/Cluster/Plan Kubernetes CIDR blocks under VPC.md#).

-   To guarantee that a Kubernetes cluster and a Swarm cluster can be connected with a VPC, you must select the same VPC when creating the Kubernetes cluster.
-   To guarantee that a Kubernetes cluster can be connected with a Swarm cluster that uses a classic network, you must migrate the Swarm cluster to a VPC. For more information, see [Migration overview](../../../../../reseller.en-US/Best practices/Migrate from the classic network to VPC/Migration overview.md#).

    After a Kubernetes cluster and a Swarm cluster are connected through a network, storage devices \(such as OSS, NAS, or RDS\) or databases in the Swarm cluster will obtain IP addresses in the VPC. That is, Kubernetes cluster applications can use these IP addresses to access corresponding storage devices or databases in the Swarm cluster over the VPC.


