---
title: Deploy TiDB in Kubernetes Using kind
summary: Learn how to deploy a TiDB cluster in Kubernetes using kind.
category: how-to
---

# Deploy TiDB in Kubernetes Using kind

This tutorial shows how to deploy [TiDB Operator](https://github.com/pingcap/tidb-operator) and a TiDB cluster in Kubernetes on your laptop (Linux or macOS) using [kind](https://kind.sigs.k8s.io/).

kind is a tool for running local Kubernetes clusters using Docker containers as cluster nodes. It is developed for testing local Kubernetes clusters, initially targeting the conformance tests. The Kubernetes cluster version depends on the node image that your kind uses, and you can specify the image to be used for the nodes and choose any other published version. Refer to [Docker hub](https://hub.docker.com/r/kindest/node/tags) to see available tags.

> **Warning:**
>
> This deployment is for testing only. DO NOT USE in production!

## Prerequisites

Before deployment, make sure the following requirements are satisfied:

- Resources requirement: 2 CPU cores+, Memory 4G+

    > **Note:**
    >
    > For macOS, you need to allocate at least 2 CPU cores and 4G Memory to Docker. For details, see [Docker configuration for Mac](https://docs.docker.com/docker-for-mac/#advanced).

- [Docker](https://docs.docker.com/install/): version >= 17.03

- [Helm Client](https://github.com/helm/helm/blob/master/docs/install.md#installing-the-helm-client): version >= 2.9.0 and < 3.0.0

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl): version >= 1.10 (1.13 or later recommended)

    > **Note:**
    >
    > The output might vary slightly among different versions of `kubectl`.

- [kind](https://kind.sigs.k8s.io/docs/user/quick-start/): version >= 0.4.0
- The value of [net.ipv4.ip_forward](https://linuxconfig.org/how-to-turn-on-off-ip-forwarding-in-linux) should be set to `1`

## Step 1: Create a Kubernetes cluster using kind

First, make sure that Docker is running. Then, you can create a local Kubernetes cluster with a script in our repository. Follow the steps below:

1. Clone the code:

    {{< copyable "shell-regular" >}}

    ``` shell
    git clone --depth=1 https://github.com/pingcap/tidb-operator && \
    cd tidb-operator
    ```

2. Run the script and create a local Kubernetes cluster:

    {{< copyable "shell-regular" >}}

    ``` shell
    hack/kind-cluster-build.sh
    ```

    > **Note:**
    >
    > By default, this script starts a Kubernetes cluster of the 1.12.8 version, with six nodes in the cluster and for each node the number of mount points is 9. You can configure these items by startup options.
    >
    > {{< copyable "shell-regular" >}}
    >
    > ```shell
    > hack/kind-cluster-build.sh --nodeNum 2 --k8sVersion v1.14.6 --volumeNum 3
    > ```

3. To connect the local Kubernetes cluster, set the default configuration file path of kubectl to `kube-config`.

    {{< copyable "shell-regular" >}}

    ```shell
    export KUBECONFIG="$(kind get kubeconfig-path)"
    ```

4. Verify whether the Kubernetes cluster is on and running:

    {{< copyable "shell-regular" >}}

    ``` shell
    kubectl cluster-info
    ```

    The output is like this:

    ``` shell
    Kubernetes master is running at https://127.0.0.1:50295
    KubeDNS is running at https://127.0.0.1:50295/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
    ```

5. Check the `storageClass` of the cluster:

    {{< copyable "shell-regular" >}}

    ``` shell
    kubectl get storageClass
    ```

    The output is like this:

    ``` shell
    NAME                 PROVISIONER                    AGE
    local-storage        kubernetes.io/no-provisioner   7m50s
    standard (default)   kubernetes.io/host-path        8m29s
    ```

## Step 2: Deploy TiDB Operator in the Kubernetes cluster

Refer to the steps in [Deploy TiDB Operator](/dev/tidb-in-kubernetes/deploy/tidb-operator.md#install-tidb-operator).

## Step 3: Deploy a TiDB cluster in the Kubernetes cluster

Refer to the steps in [Deploy TiDB on General Kubernetes](/dev/tidb-in-kubernetes/deploy/general-kubernetes.md#deploy-tidb-cluster).

## Access the database and monitoring dashboards

Refer to the steps in [View the monitoring dashboard](/dev/tidb-in-kubernetes/monitor/tidb-in-kubernetes.md#view-the-monitoring-dashboard).

## Destroy the TiDB and Kubernetes cluster

To destroy the local TiDB cluster, refer to the steps in [Destroy TiDB Clusters in Kubernetes](/dev/tidb-in-kubernetes/maintain/destroy-tidb-cluster.md).

To destroy the Kubernetes cluster, execute the following command:

{{< copyable "shell-regular" >}}

``` shell
kind delete cluster
```