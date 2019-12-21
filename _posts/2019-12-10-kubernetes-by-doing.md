---
layout: post
title: Kubernetes by doing
---


# {{ page.title }}

Setting up a new Kubernetes cluster

## Initialization

We'll setup the following:

* Install Docker on three nodes
* Install Kubeadm, Kubelet, and Kubectl on all three nodes
* Bootstrap the cluster on the Kube master node
* Join the two Kube worker nodes to the cluster
* Setup cluster networking with __flannel__

### Install Docker

Install Docker on all three nodes

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
    sudo apt-get update
    sudo apt-get install -y docker-ce=18.06.1~ce~3-0~ubuntu
    sudo apt-mark hold docker-ce

Verify Docker is up and running

    sudo systemctl status docker

### Install Kubeadm, Kubelet, and Kubectl

Install __Kubeadm__ (a tool built to provide `kubeadm init` and `kubeadm join` as best-practice fast paths for
creating Kubernetes clusters). kubeadm performs the actions to get a minimum viable cluster up and running with the
focus on bootstrapping and not about provisioning machines. https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm/

  * `kubeadm init` bootstraps a Kubernetes control-plane node
  * `kubeadm join` bootstraps a Kubernetes worker node and join it to the cluster

Install __Kubelet__ (the primary "node agent" that runs on each node). Kubelet's job is to make sure that containers
are running in a pod. Kubelet can register the node with the apiserver using one of the following:
the hostname, a flag to override the hostname, or specific logic for a cloud provider
https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/

Install __Kubectl__ (a command line interface for running commands against Kubernetes clusters)
https://kubernetes.io/docs/reference/kubectl/overview/

### Bootstrap the cluster on the Kube master node

On the Kube master node only, run:

    sudo kubeadm init --pod-network-cidr=10.244.0.0/16

Note: you will get a `kubeadm join` command that gets printed out (this is for the two Kube workers to join the cluster later)

Setup the local __kubeconfig__:

    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config

Verify the Kube master node is up with:

    kubectl version
    Client Version: version.Info{Major:"1", Minor:"12", GitVersion:"v1.12.7", GitCommit:"6f482974b76db3f1e0f5d24605a9d1d38fad9a2b", GitTreeState:"clean", BuildDate:"2019-03-25T02:52:13Z", GoVersion:"go1.10.8", Compiler:"gc", Platform:"linux/amd64"}
    Server Version: version.Info{Major:"1", Minor:"12", GitVersion:"v1.12.10", GitCommit:"e3c134023df5dea457638b614ee17ef234dc34a6", GitTreeState:"clean", BuildDate:"2019-07-08T03:40:54Z", GoVersion:"go1.10.8", Compiler:"gc", Platform:"linux/amd64"}

### Join the two Kube worker nodes to the cluster

On the two worker nodes, join the master

    sudo su
    kubeadm join $some_ip:6443 --token $some_token --discovery-token-ca-cert-hash $some_hash

Check that your nodes joined the cluster (will have status `NotReady`)

    kubectl get nodes

    NAME            STATUS     ROLES    AGE   VERSION
    ip-10-0-1-101   NotReady   master   35m   v1.12.7
    ip-10-0-1-102   NotReady   <none>   18s   v1.12.7
    ip-10-0-1-103   NotReady   <none>   10s   v1.12.7

### Set up cluster networking with flannel

Turn on iptables bridge calls on all three nodes:

    echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf
    sudo sysctl -p

On the Kubernetes master node only, install Flannel

    kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml

    clusterrole.rbac.authorization.k8s.io/flannel created
    clusterrolebinding.rbac.authorization.k8s.io/flannel created
    serviceaccount/flannel created
    configmap/kube-flannel-cfg created
    daemonset.extensions/kube-flannel-ds-amd64 created
    daemonset.extensions/kube-flannel-ds-arm64 created
    daemonset.extensions/kube-flannel-ds-arm created
    daemonset.extensions/kube-flannel-ds-ppc64le created
    daemonset.extensions/kube-flannel-ds-s390x created

Get nodes (should now have status 'Ready')

    kubectl get nodes
    NAME            STATUS   ROLES    AGE     VERSION
    ip-10-0-1-101   Ready    master   43m     v1.12.7
    ip-10-0-1-102   Ready    <none>   8m19s   v1.12.7
    ip-10-0-1-103   Ready    <none>   8m11s   v1.12.7

## Deploy a Service to Kubernetes

We want to:

* create a deployment for our service
* create a service and verify that you can access it

### Create a deployment for our service

Log into the Kube master node and create a deployment (which will state four replias):

    cat << EOF | kubectl apply -f -
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: store-products
      labels:
        app: store-products
    spec:
      replicas: 4
      selector:
        matchLabels:
          app: store-products
      template:
        metadata:
          labels:
            app: store-products
        spec:
          containers:
          - name: store-products
            image: linuxacademycontent/store-products:1.0.0
            ports:
            - containerPort: 80
    EOF

### Create a service

Create a service for our pods

    cat << EOF | kubectl apply -f -
    kind: Service
    apiVersion: v1
    metadata:
      name: store-products
    spec:
      selector:
        app: store-products
      ports:
      - protocol: TCP
        port: 80
        targetPort: 80
    EOF

Make sure the service is up in the cluster:

    kubectl get svc store-products

Query the service (store-products) from another machine (busybox)

    kubectl exec busybox -- curl -s store-products

## Deploy a microservice application to Kubernetes

Kubernetes lets you deploy a microservice architecture to a cluster and independently scale specific components.

### Stan's Robot Shop

We will use Instana's open source `Stan's Robot Shop` as a sample microservice application
https://www.instana.com/blog/stans-robot-shop-sample-microservice-application/

For local development, you can just run a `docker-compose up` and see containers that look like:

    docker ps
    CONTAINER ID        IMAGE                                    COMMAND                  CREATED              STATUS              PORTS                                                 NAMES
    280dd3f23bb5        linuxacademycontent/rs-web:0.3.2         "/root/entrypoint.sh"    About a minute ago   Up About a minute   80/tcp, 0.0.0.0:8080->8080/tcp                        robot-shop_web_1
    1cda4525023f        linuxacademycontent/rs-shipping:0.3.2    "java -Xmn256m -Xmx7…"   About a minute ago   Up About a minute   8080/tcp                                              robot-shop_shipping_1
    f6edbe16b538        linuxacademycontent/rs-payment:0.3.2     "python payment.py"      About a minute ago   Up About a minute   8080/tcp                                              robot-shop_payment_1
    1c52bd4ee4b1        linuxacademycontent/rs-ratings:0.3.2     "docker-php-entrypoi…"   About a minute ago   Up About a minute   80/tcp                                                robot-shop_ratings_1
    a1d777a51a0c        linuxacademycontent/rs-dispatch:0.3.2    "/bin/sh -c bin/gorcv"   About a minute ago   Up About a minute                                                         robot-shop_dispatch_1
    a9c61ad074d5        linuxacademycontent/rs-user:0.3.2        "docker-entrypoint.s…"   About a minute ago   Up About a minute   8080/tcp                                              robot-shop_user_1
    74d96e05674c        linuxacademycontent/rs-cart:0.3.2        "docker-entrypoint.s…"   About a minute ago   Up About a minute   8080/tcp                                              robot-shop_cart_1
    3c84ff6cce75        linuxacademycontent/rs-catalogue:0.3.2   "docker-entrypoint.s…"   About a minute ago   Up About a minute   8080/tcp                                              robot-shop_catalogue_1
    be8d2e6b5a6a        redis:4.0.6                              "docker-entrypoint.s…"   About a minute ago   Up About a minute   6379/tcp                                              robot-shop_redis_1
    fd94d6a937c6        rabbitmq:3.7-management-alpine           "docker-entrypoint.s…"   About a minute ago   Up About a minute   4369/tcp, 5671-5672/tcp, 15671-15672/tcp, 25672/tcp   robot-shop_rabbitmq_1
    647f62e5fb74        linuxacademycontent/rs-mysql-db:0.3.2    "docker-entrypoint.s…"   About a minute ago   Up About a minute   3306/tcp                                              robot-shop_mysql_1
    60df63c9ef0b        linuxacademycontent/rs-mongodb:0.3.2     "docker-entrypoint.s…"   About a minute ago   Up About a minute   27017/tcp                                             robot-shop_mongodb_1

To run this on kubernetes, you can create your own k8s namespace like so:

    kubectl create namespace robot-shop

