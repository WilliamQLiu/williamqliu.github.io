---
layout: post
title: Kubernetes
---


# {{ page.title }}

## Background

Container images are used to confine application code, its runtime, and all of its dependences in a
predefined format. With container runtimes like __runC__, __containerd__, or __rkt__, we can use
those prepackaged images to create one or more containers. All of these runtimes are good at running
containers on a single host (e.g. a dev environment), but in practice we want to have a fault-tolerant
and scalable solution by creating a single __controller/management unit__ that connects multiple nodes
together; this unit is called the __container orchestrator__.

### Containers

__Containers__ are an application-centric way to deliver high performing, scalable applications
on the infrastructure of your choice. You can run a container of say a Django web app or Nginx load
balancer on your desktop or the cloud or a vm or on premise.

__Container Image__ is when an application is bundled along with its runtime and dependencies.
We can use the image to create an isolated executable environment, also known as container.

### Resources

Run `kubectl api-resources` to see what resources are available along with their shortnames, e.g.

```
NAME                              SHORTNAMES   APIVERSION                             NAMESPACED   KIND
bindings                                       v1                                     true         Binding
componentstatuses                 cs           v1                                     false        ComponentStatus
configmaps                        cm           v1                                     true         ConfigMap
endpoints                         ep           v1                                     true         Endpoints
events                            ev           v1                                     true         Event
limitranges                       limits       v1                                     true         LimitRange
namespaces                        ns           v1                                     false        Namespace
nodes                             no           v1                                     false        Node
persistentvolumeclaims            pvc          v1                                     true         PersistentVolumeClaim
persistentvolumes                 pv           v1                                     false        PersistentVolume
pods                              po           v1                                     true         Pod
podtemplates                                   v1                                     true         PodTemplate
replicationcontrollers            rc           v1                                     true         ReplicationController
resourcequotas                    quota        v1                                     true         ResourceQuota
secrets                                        v1                                     true         Secret
serviceaccounts                   sa           v1                                     true         ServiceAccount
services                          svc          v1                                     true         Service
mutatingwebhookconfigurations                  admissionregistration.k8s.io/v1        false        MutatingWebhookConfiguration
validatingwebhookconfigurations                admissionregistration.k8s.io/v1        false        ValidatingWebhookConfiguration
customresourcedefinitions         crd,crds     apiextensions.k8s.io/v1                false        CustomResourceDefinition
apiservices                                    apiregistration.k8s.io/v1              false        APIService
controllerrevisions                            apps/v1                                true         ControllerRevision
daemonsets                        ds           apps/v1                                true         DaemonSet
deployments                       deploy       apps/v1                                true         Deployment
replicasets                       rs           apps/v1                                true         ReplicaSet
statefulsets                      sts          apps/v1                                true         StatefulSet
tokenreviews                                   authentication.k8s.io/v1               false        TokenReview
localsubjectaccessreviews                      authorization.k8s.io/v1                true         LocalSubjectAccessReview
selfsubjectaccessreviews                       authorization.k8s.io/v1                false        SelfSubjectAccessReview
selfsubjectrulesreviews                        authorization.k8s.io/v1                false        SelfSubjectRulesReview
subjectaccessreviews                           authorization.k8s.io/v1                false        SubjectAccessReview
horizontalpodautoscalers          hpa          autoscaling/v2                         true         HorizontalPodAutoscaler
cronjobs                          cj           batch/v1                               true         CronJob
jobs                                           batch/v1                               true         Job
certificatesigningrequests        csr          certificates.k8s.io/v1                 false        CertificateSigningRequest
leases                                         coordination.k8s.io/v1                 true         Lease
strimzipodsets                    sps          core.strimzi.io/v1beta2                true         StrimziPodSet
endpointslices                                 discovery.k8s.io/v1                    true         EndpointSlice
events                            ev           events.k8s.io/v1                       true         Event
flowschemas                                    flowcontrol.apiserver.k8s.io/v1beta2   false        FlowSchema
prioritylevelconfigurations                    flowcontrol.apiserver.k8s.io/v1beta2   false        PriorityLevelConfiguration
kafkabridges                      kb           kafka.strimzi.io/v1beta2               true         KafkaBridge
kafkaconnectors                   kctr         kafka.strimzi.io/v1beta2               true         KafkaConnector
kafkaconnects                     kc           kafka.strimzi.io/v1beta2               true         KafkaConnect
kafkamirrormaker2s                kmm2         kafka.strimzi.io/v1beta2               true         KafkaMirrorMaker2
kafkamirrormakers                 kmm          kafka.strimzi.io/v1beta2               true         KafkaMirrorMaker
kafkarebalances                   kr           kafka.strimzi.io/v1beta2               true         KafkaRebalance
kafkas                            k            kafka.strimzi.io/v1beta2               true         Kafka
kafkatopics                       kt           kafka.strimzi.io/v1beta2               true         KafkaTopic
kafkausers                        ku           kafka.strimzi.io/v1beta2               true         KafkaUser
ingressclasses                                 networking.k8s.io/v1                   false        IngressClass
ingresses                         ing          networking.k8s.io/v1                   true         Ingress
networkpolicies                   netpol       networking.k8s.io/v1                   true         NetworkPolicy
runtimeclasses                                 node.k8s.io/v1                         false        RuntimeClass
catalogsources                    catsrc       operators.coreos.com/v1alpha1          true         CatalogSource
clusterserviceversions            csv,csvs     operators.coreos.com/v1alpha1          true         ClusterServiceVersion
installplans                      ip           operators.coreos.com/v1alpha1          true         InstallPlan
olmconfigs                                     operators.coreos.com/v1                false        OLMConfig
operatorconditions                condition    operators.coreos.com/v2                true         OperatorCondition
operatorgroups                    og           operators.coreos.com/v1                true         OperatorGroup
operators                                      operators.coreos.com/v1                false        Operator
subscriptions                     sub,subs     operators.coreos.com/v1alpha1          true         Subscription
packagemanifests                               packages.operators.coreos.com/v1       true         PackageManifest
poddisruptionbudgets              pdb          policy/v1                              true         PodDisruptionBudget
clusterrolebindings                            rbac.authorization.k8s.io/v1           false        ClusterRoleBinding
clusterroles                                   rbac.authorization.k8s.io/v1           false        ClusterRole
rolebindings                                   rbac.authorization.k8s.io/v1           true         RoleBinding
roles                                          rbac.authorization.k8s.io/v1           true         Role
priorityclasses                   pc           scheduling.k8s.io/v1                   false        PriorityClass
csidrivers                                     storage.k8s.io/v1                      false        CSIDriver
csinodes                                       storage.k8s.io/v1                      false        CSINode
csistoragecapacities                           storage.k8s.io/v1                      true         CSIStorageCapacity
storageclasses                    sc           storage.k8s.io/v1                      false        StorageClass
volumeattachments                              storage.k8s.io/v1                      false        VolumeAttachment
```

Short names can be used in: `kubectl get <shortname`, e.g. `kubectl get sc`

### Dev/QA vs Production

We can use a single host to develop and test applications, but we can't do this on a live environment
because we need to make sure that our applications are:

* fault-tolerant
* can scale (on-demand)
* uses resources optimally
* can discover and communicate with other applications automatically
* are accessible to the outside world
* can update and rollback without any downtime

### Container Orchestrators

__Container Orchestrators__ are the tools that group host together to form a cluster and run containers
on production. A few examples include:

* Docker Swarm, provided by Docker (part of Docker Engine)
* Kubernetes, started by Google, now owned by Cloud Native Computing Foundation
* Mesos Marathon (runs on Apache Mesos)
* Amazon ECS (runs Docker containers)
* Nomad by Hashicorp

### Why Container Orchestrators?

You can run and scale containers manually with scripts, but its a lot easier with orchestration tools

# Installation

Install Docker, Minikube, kubectl and VirtualBox

# CNCF

__CNCF__ (cncf.io) is a sub foundation of the Linux Foundation
Kubernetes is the 'anchor' project
Check out Linux Foundation's course on edx for Kubernetes (4 hours)
Local Kubernetes via Minikube are some basic deployments

# Kubernetes

Kubernetes (aka __k8s__) stands for 'helmsman' or 'ship pilot';
it is an open-source system for automating deployment, scaling, and management of containerized applications.

There are new releases every 3 months.

## What is it

Container Orchestration

Similar to:

* Docker Swarm
* Mesos Marathon
* AWS ECS
* Hashicorp Nomad

## Why?

Kubernetes is the whole package instead of doing one piece and needing other pieces (e.g. Ansible + Ansible Vault for secrets)

* Automate container deployment across instances
* Schedule deployments
* Manage inter-container communication
* Group/organize containers
* Self-healing (automatically replaces and reschdules containers from failed nodes; auto kills and restarts containers that
do not respond to health checks)
* Automatic binpacking (automatically schedules the containers based on resource usage and constraints while keeping availability)
* Service discovery and load balancing - groups of containers refer to each other via Domain Name System (DNS) / Kubernetes service.

## Features

See it has it all, even key-value storage for env variables

Binpacking
Self-healing
Scaling
Service Discovery/Load Balancing
Automated Rollouts/backs
Secrets/Config management
Storage
Batch execution (and long running jobs)

## General Architecture

Kubernetes has the following main components:

* One or more Master Nodes (can have multiple masters for high availablity w/ one as a leader)
  On the __Master Node__, we have an __API Server__, a __Scheduler__ and __Controller__, (e.g.
  https://kubernetes.io/docs/concepts/overview/components/ with kube-controller-manager, cloud-controller-manager,
  kube-apiserver, etcd, and kube-scheduler)
* One or more Worker Nodes
  On the Worker Node, we have a __kube-proxy__, a __Kublet__, and __pods__
* Distributed key-value store (__etcd__ is default), usually connected through Master Node

User interacts with the Master Node(s) through a CLI/API/Dashboard

## Master Node

The __master node__ is response for managing the Kubernetes cluster.
It is the entry point for all administrative tasks.

* Users communicate to master node via API Server, CLI, or the GUI (Dashboard)
* __Scheduler__ interacts with workers
* __Controller Manager__ manages state of cluster

If there is multiple master nodes, they would be in __high availability (HA) mode__. One would be
the leader and performs all operations, the others would be followers.

To manage the cluster state, Kubernetes uses __etcd__, a distributed key-value store. This
distributed key-value store holds the state of the cluster.

### Scheduler

The __scheduler__ schedules the work to different worker nodes. The scheduler has the resource
usage information for each worker node. It knows the constraints that users/operators may have set,
such as scheduling work on a node that has the label `disk==ssd`.

The scheduler schedules the work in terms of __Pods__ and __Services__.

### Controller Manager

The __controller manager__ manages different non-terminating control loops, which regulate the
state of the Kubernetes cluster. Each control loop knows the desired state of the objects it
manages by watching their current state through the API server. If the current state of the object
does not match the desired state, the control loop takes corrective steps.

### etc

__etcd__ is a distributed key-value store that stores the state of the cluster. It can be
configured externally (with Master nodes connecting to it)  or as part of the Kubernetes Master Node.

## Worker Nodes

A __worker node__ is a machine that runs the applications using __Pods__ and is controlled by the
master node. Pods are scheduled on the worker nodes. A __Pod__ is the scheduling unit in Kubernetes.
It is a logical collection of one or more containers that are always scheduled together (e.g. a group
of apps together). A worker node has:

* __container runtime__, what runs under the hood of the container
* __kubelet__, an agent on each worker node that communicates with the master node
* __kube-proxy__, a proxy that routes requests

### Container Runtime

A __Container runtime__ (default is Docker, can be __rkt__). Others include __containerd__, __lxd__.
Technically __docker__ is a platform to run containers; it runs __containerd__ as a container runtime

### Kubelet

The __kubelet__ is an agent that runs on each worker node and manages pods within runtime (i.e. communicates
with the master node). It runs the containers associated with the Pod. The kubelet connects to the container
runtime using __Container Runtime Interface (CRI)__.

#### Kubelet - CRI

The __Container Runtime Interface (CRI)__ consists of:

* protocol buffers
* gRPC API
* libraries

The kubelet (grpc client) connects to the CRI shim (grpc server) to perform container and image operations.
CRI implements two services: __ImageService__ and __RuntimeService__.

* __ImageService__ is responsible for all the image-related operations
* __RuntimeService__ is responsible for all the Pod and container-related operations

#### Kubelet - CRI Shims

Some examples of CRI Shims include:

* __dockershim__ is where containers are created using Docker and installed on worker nodes with the following:
    dockershim - Kubelet - CRI - dockershim - docker - containerd - container
* __cri-containerd__ is where we can directly use Docker's smaller core of __containerd__ to create and manage containers
    cri-containerd - kubelet - CRI - cri-conntainerd - containerd - container
* __CRI-O__ enables any __Open Container Initiative (OCI)__ compatible runtimes with Kubernetes. Examples
 include __runC__ and __Clear Containers__ as container runtimes.

### Kube-Proxy

Instead of connecting directly to Pods to access applications, we use a logical construct called a __Service__
as a connection endpoint. A Service groups related Pods and when accessed, load balances to them.

The __kube-proxy__ is the network proxy that runs on each worker node and that routes requests.
For each Service endpoint, kube-proxy sets up the routes so that it can reach to it.

We expose our services to the external world with kube-proxy so we can access our applications from
outside the cluster.

### etcd

__etcd__ stores the cluster state. etcd is a distributed key-value stored based on the __Raft Consensus Algorithm__.

etcd is written in Go and is capable of storing configuration details like subnets, ConfigMaps, Secrets, etc.

## Network

A fully functioning Kubernetes cluster needs the following:

* A unique IP assigned to each Pod
* Containers in a Pod can communicate to each other
* The Pod is able to communicate with other Pods in the cluster
* If configured, the application deployed inside a Pod is accessible from the external world

### Assign a unique IP Address to each Pod

Each Pod gets a unique IP Address. For container networking, there are two primary specifcations:

* __Container Network Model (CNM)__, proposed by Docker
* __Container Network Interface (CNI)__, proposed by CoreOS

Currently Kubernetse uses CNI to assign the IP Address to each Pod.
The container runtime offloads the IP assignment to CNI, which connects to the underlying
configured plugin like LoopBack, Bridge, IPvlan or MACvlan to get the IP address. Once the IP
address is given by the plugin, CNI forwards it back to the requested container runtime.

### Communication between Containers inside a Pod

All the container runtimes create an isolated network entity for each container that it starts.
On Linux, the entiy is a network namespace. This network namespace can be shared across containers
or with the host operating system.

Inside a Pod, containers share the network namespace so they can reach to each other via localhost.

### Communication between Pods across Nodes

In a clustered environment, Pods can be scheduled on any node. We want to make sure that the
Pods can communicate across the nodes and all nodes should be able to reach any Pod.
Kubernetes puts in a condition that there shouldn't be any __Network Address Translation (NAT)__
while doing Pod-to-Pod communication across hosts.

## Kubernetes Configurations

Kubernetes can be installed using different configurations, with the four major types:

* All-in-One Single-Node - everything installed on a single node, good for learning only (e.g. Minikube)
* Single-Node etcd, Single-Master, and Multi-Worker
* Single-Node etcd, Multi-Master, and Multi-Worker
* Multi-Node etcd, Multi-Master, and Multi-Worker

## Kubernetes Install

Kubernetes can be installed on-premise (on VMs and bare metal) or through the cloud

### Kubernetes Install On-Premise

On-Premise Installation on VMs via Vagrant, VMware vSphere, KVM, etc. Can use
tools like __Ansible__ or __kubeadm__.

On-Premise Installation on Bare Metal on top of different operating systems. Can
use same tools as above.

### Kubernetes Install on Cloud

You can install on the cloud with:

### Hosted Solutions

Software is managed by the provider. Examples include:

* Google Kubernetes Engine (GKE)
* Azure Container Service (AKS)
* Amazon Elastic Container Service for Kubernetes (EKS)
* OpenShift Dedicated
* Platform9
* IBM Cloud Container Service

### Turnkey Cloud Solutions

Kubernetes can be installed in a few commands

* Google Compute Engine
* Amazon AWS
* Microsfot Azure
* Tectonic by CoreOS

### Bare Metal

Install Kubernetes on bare metal provided by cloud providers

## Installation Tools

* __kubeadm__
* __KubeSpray__
* __Kops__

### kubeadm

__kubeadm__ is a first-class citizen in the Kubernetes ecosystem. It is the recommended way
to bootstrap the Kubernetes cluster.

### KubeSpray

With __KubeSpray__ (formerly __Kargo__) we can install highly available Kubernetes clusters on
AWS, GCE, Azure, OpenStack, or bare metal. KubeSpray is based on Ansible.

### Kops

With __Kops__ we can create, destroy, upgrade, and maintain production-grade, high-available
Kubernetes clusters from the command line. It can provision the machines as well.

Check out Kelsey Hightower's Kubernetes The Hard Way GitHub project.


## Components

An overview of components are:

* pods
* deployments
* services

### Pods

A __Pod__ is the smallest and simplest Kubernetes object.

A logical set of one or more containers
Like Docker-Compose Up
Scheduled on same host
Share network namespace
Mount same volumes (same external storage)
Can be labeled
ReplicaSet represents pod group (say I want 4 of these types of pods, is a ReplicaSet)

```
# Get a list of pods for a namespace
kubectl -n mynamespace get pods
NAME                                          READY   STATUS    RESTARTS   AGE
mypod-7w7tx                                     3/3     Running   0          171m

# Describe a pod in detail
kubectl -n mynamespace describe pod mypod-7w7tx

# shell into pod
kubectl -n mynamespace exec -it mypod-7w7tx bash

# Get logs from all containers
kubectl -n mynamespace logs -f somecontainer --all-containers
```

```
# Get the yaml of a pod
kubectl -n mynamespace get pod mypod --output=yaml
```

### Deployments

Deployments bind Replica Sets and Pod templates
E.g. I make a deployment of 3 nginx containers
 * Creates ReplicaSetA
I update the nginx version in the deployment
 * Creates ReplicaSetB and rolls over service

```
# Get all deployments
kubectl -n mynamespace get deployments

# Restart a deployment
kubectl -n mynamespace rollout restart deployment mydeployment
```

### Services

What you access (done via labels)
Group pods logically for access
This is what clients interact with
Created via Label Selection
app=api env=dev would create a service for all our api containers in the dev environment

```
kubectl -n mynamespace get services
NAME                TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
my-service          NodePort    X.X.X.X         <none>      5000:30103/TCP   2d
```

#### Service Types

How do you want to send traffic to a single location
E.g. pick one of these types:

* ClusterIP and NodePort - reach containers via an assigned ip address or a specifc port on the cluster's ip
* LoadBalancer - hook Kubernetes up to a service

## Containers

Use __alpine__ for a very minimal container; use this for your container

## Docker

Docker creates a cached layer of a virtual machine

## Minikube

Allows you to create an all-in-one Kubernetes setup.

### Minikube Installation Requirements

Usually Minikube runs inside a VM on Linux, Mac, or Windows. We need to make sure that we have the
supported hardware and the hypervisor to create VMs. We'll need:

* kubectl - a binary used to access any Kubernetes cluster
* On Linux - Virtualbox or KVM hypervisors (i.e. virtual machine monitors)
* VT -x/AMD-v virtualization must be enabled in BIOS

Install kubectl

	curl -Lo kubectl
	https://storage.googleapis.com/kubernetes-release/release/v1.9.0/bin/linux/amd64/kubectl && chmod +x
	kubectl && sudo mv kubectl /usr/local/bin/

Install the Hypervisor (Virtualbox) with:

	$ sudo apt-get install virtualbox

Install Minikube

	$ curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.25.0/minikube-linux-amd64 &&
	$ chmod +x minikube && sudo mv minikube /usr/local/bin/

### Minikube Start

Run with: `minikube start`, will see:

minikube config set WantUpdateNotification false
Starting local Kubernetes v1.7.0 cluster...
Starting VM...
Getting VM IP address...
Moving files into cluster...
Setting up certs...
Starting cluster components...
Connecting to cluster...
Setting up kubeconfig...
Kubectl is now configured to use the cluster.

### Minikube Status

Check status with: `minikube status`

minikube: Running
localkube: Running
kubectl: Correctly Configured: pointing to minikube-vm at 192.168.99.100

### Accessing Minikube

You can access a running Kubernetes cluster via any of the following methods:

* Command Line Interface (CLI)
* Graphical User Interface (GUI)
* APIs

#### Accessing Minikube through the Command Line Interface (CLI)

__kubectl__ is the __command line interface (cli)__ tool to manage the Kubernetes cluster resources
and applications. We can also use kubectl to deploy our applications.

#### Accessing Minikube through the Graphical User Interface (GUI)

See a web gui dashboard with: `minikube dashboard`

#### Accessing Minikube through the APIs

Kubernetes has an API Server and operators/uesrs can connect to it from the external world to interact
with the cluster. We can directly connect to the API server using its API endpoints and send commands to it,
as long as we can access the master node and have the right credentials. The API space for Kubernetes is:

	/
	/healthz	/metrics	/api	/apis
	/api/v1/pods	/api/v1/nodes	/api/v1/services
	/apis/apps/v1/Depolyment	/apis/apps/v1/DaemonSet	/apis/apps/v1/StatefulSet

The API space can be divided into three independent groups:

* __Core Group__ includes objects such as Pods, Services, nodes, etc.
* __Named Group__ includes objects in `/api/$NAME/$VERSION` format, e.g.
	`/apis/batch/v2alpha1` - __alpha__ may be dropped at any point
    `/apis/certifications.k8s.io/v1beta1` - __beta__ means well tested, but semantics of object may change
    `/apis/networking.k8s.io/v1` - __stable__ means in released software
* __System-Wide__ consists of system-wide API endpoints like `/healthz`, `/logs`, `/metrics`, `/ui`

We can connect to the API server directly via calling the respective API endpoints or via CLI/GUI.

## kubectl

__kubect__ allows access to a Kubernetes cluster through the command line.

### kubectl Configuration File

To connect to the Kubernetes cluster, kubectl needs the master node endpoint and the credentials to connect to it.
When starting Minikube, the process creates a configuration file __config__ inside the __.kube__ directory, which
sits in the user's __home__ directory. The connfiguration file has all the connection details.
The kubectl binary accesses this file to find the master node's connection endpoint along with the credentials.
To view the file, check __~/.kube/config__ or run the command:

	$kubectl config view
	apiVersion: v1
	clusters:
	- cluster:
	    certificate-authority: /home/will/.minikube/ca.crt
	    server: https://192.168.99.100:8443
	  name: minikube
	contexts:
	- context:
	    cluster: minikube
	    user: minikube
	  name: minikube
	current-context: minikube
	kind: Config
	preferences: {}
	users:
	- name: minikube
	  user:
	    client-certificate: /home/will/.minikube/client.crt
	    client-key: /home/will/.minikube/client.key

### kubectl cluster-info

	$ kubectl cluster-info
	Kubernetes master is running at https://192.168.99.100:8443

### kubectl proxy

__kubectl__ will authenticate with the API server on the master node and make the dashboard available with:

	$ kubectl proxy

Then you can see the dashboard on:

	http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy/#!/overview?namespace=default

The service will be called __kubernetes-dashboard__ inside the __kube-system__ namespace.

### APIs with kubectl proxy

With __kubectl proxy__ configured, we can send requests to __localhost__ on the __proxy__ port:

Here we requested all the API endpoints from the API server.

	$ curl http://localhost:8001/
	{
	 "paths": [
	   "/api",
	   "/api/v1",
	   "/apis",
	   "/apis/apps",
	   ......
	   ......
	   "/logs",
	   "/metrics",
	   "/swaggerapi/",
	   "/ui/",
	   "/version"
	 ]
	}%

### APIs without kubectl proxy

Without the kubectl proxy configured, we can instead get a __Bearer Token__ using kubectl, then send
that along with our API request. A Bearer Token is an __access token__ generated by the
authentication server (the API server on the master node) and given back to the client. Using that
token, the client can connect back to the Kubernetes API without providing additional authentication
details and can then access resources.

GET the token with:

	$ TOKEN=$(kubectl describe secret $(kubectl get secrets | grep default | cut -f1 -d ' ') | grep -E '^token' | cut -f2 -d':' | tr -d '\t' | tr -d " ")

GET the API server endpoint (make sure APIServer is pointing to your Minikube's IP)

	$ APISERVER=$(kubectl config view | grep https | cut -f 2- -d ":" | tr -d " ")

	$ echo $APISERVER
	https://192.168.99.100:8443

Access the API Server using curl:

	$ curl $APISERVER --header "Authorization: Bearer $TOKEN" --insecure
	{
	 "paths": [
	   "/api",
	   "/api/v1",
	   "/apis",
	   "/apis/apps",
	   ......
	   ......
	   "/logs",
	   "/metrics",
	   "/swaggerapi/",
	   "/ui/",
	   "/version"
	 ]
	}%

### kubectl GET deployments

	$kubectl get deployments
	NAME             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
	hello-minikube   1         1         1            1           208d

	$kubectl get replicasets
	NAME                       DESIRED   CURRENT   READY     AGE
	hello-minikube-180744149   1         1         1         208d

	$kubectl get pods
	NAME                             READY     STATUS    RESTARTS   AGE
	hello-minikube-180744149-sjp84   1/1       Running   1          208d


	$kubectl describe pods
	Name:		hello-minikube-180744149-sjp84
	Namespace:	default
	Node:		minikube/192.168.99.100
	Start Time:	Thu, 24 Aug 2017 20:09:53 -0600
	Labels:		pod-template-hash=180744149
			run=hello-minikube
	Annotations:	kubernetes.io/created-by={"kind":"SerializedReference","apiVersion":"v1","reference":{"kind":"ReplicaSet","namespace":"default","name":"hello-minikube-180744149","uid":"7bf06d75-893a-11e7-a63e-080027f...
	Status:		Running
	IP:		172.17.0.2
	Created By:	ReplicaSet/hello-minikube-180744149
	Controlled By:	ReplicaSet/hello-minikube-180744149
	Containers:
	  hello-minikube:
		Container ID:	docker://7d691fffa7596e0803106d17bf2fe12d1582e8b2695b4277969ee40f6bf16114
		Image:		gcr.io/google_containers/echoserver:1.4
		Image ID:		docker-pullable://gcr.io/google_containers/echoserver@sha256:5d99aa1120524c801bc8c1a7077e8f5ec122ba16b6dda1a5d3826057f67b9bcb
		Port:		8080/TCP
		State:		Running
		  Started:		Wed, 21 Mar 2018 18:48:56 -0600
		Last State:		Terminated
		  Reason:		Completed
		  Exit Code:	0
		  Started:		Fri, 25 Aug 2017 07:49:54 -0600
		  Finished:		Fri, 25 Aug 2017 07:50:27 -0600
		Ready:		True
		Restart Count:	1
		Environment:	<none>
		Mounts:
		  /var/run/secrets/kubernetes.io/serviceaccount from default-token-94g57 (ro)
	Conditions:
	  Type		Status
	  Initialized 	True
	  Ready 	True
	  PodScheduled 	True
	Volumes:
	  default-token-94g57:
		Type:	Secret (a volume populated by a Secret)
		SecretName:	default-token-94g57
		Optional:	false
	QoS Class:	BestEffort
	Node-Selectors:	<none>
	Tolerations:	<none>
	Events:
	  FirstSeen	LastSeen	Count	From			SubObjectPath			Type		Reason			Message
	  ---------	--------	-----	----			-------------			--------	------			-------
	  13m		13m		1	kubelet, minikube					Normal		SuccessfulMountVolume	MountVolume.SetUp succeeded for volume "default-token-94g57"
	  13m		13m		1	kubelet, minikube					Normal		SandboxChanged		Pod sandbox changed, it will be killed and re-created.
	  13m		13m		1	kubelet, minikube	spec.containers{hello-minikube}	Normal		Pulled			Container image "gcr.io/google_containers/echoserver:1.4" already present on machine
	  13m		13m		1	kubelet, minikube	spec.containers{hello-minikube}	Normal		Created			Created container
	  13m		13m		1	kubelet, minikube	spec.containers{hello-minikube}	Normal		Started			Started container

### Infrastructure as Code

Infrastructure can also be as YAML config file

https://github.com/thedevelopnik/kubernetes-intro/blob/master/webserver.yml

`kubectl create -f webserver-service.yml`

	webserver-service.yml file

	apiVersion: extensions/v1beta1
	kind: Deployment
	metadata:
	  name: webserver
	spec:
	  replicas: 3
	  template:
	    metadata:
	      labels:
	        app: webserver
	    spec:
	      containers:
	      - name: webserver
	        image: nginx:1.13.9-alpine
	        ports:
	        - containerPort: 80

$kubectl get pods
NAME                             READY     STATUS    RESTARTS   AGE
hello-minikube-180744149-sjp84   1/1       Running   1          208d

$kubectl describe pod hello-minikube-180744149-sjp84

## kubectl create vs apply

Create - imperative management, tells kubernetes api you want to create, replace, or delete, not how
Apply - declarative management - changes that you may have applied to a live object are maintained even if you apply other changes to the object

`kubectl apply -f pingpong.py`

## Kubernetes Object Model

Kubernetes has an object model that represents various entities in the Kubernetes cluster. Entities
describe:

* What containerized applications are running and on which code
* Application resource consumption
* Different policies attached to applications, like restart/upgrade policies, fault tolerance, etc.

Each object declares our intent or desired state using the __spec__ field. Kubernetes manages the
__status__ field for objects, which it uses to record the actual state of the object.

Examples of objects include:

* Pods - simplest type of Kubernetes object
* ReplicaSets
* Deployments
* Namespaces

### Create an Object

To create an object, we provide the __spec__ field to the Kubernetes API server (saying here is the
desired state) along with other details in a __JSON__ format. We can also provide an object
definition in a __.yaml__ file, which is converted by kubectl into a JSON payload and sent to the
API server.

Example Deployment object:

	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: nginx-deployment
	  labels:
	    app: nginx
	spec:
	  replicas: 3
	  selector:
	    matchLabels:
	      app: nginx
	  template:
	    metadata:
	      labels:
	        app: nginx
	    spec:
	      containers:
	      - name: nginx
	        image: nginx:1.7.9
	        ports:
	        - containerPort: 80

### Fields for Objects

__apiVersion__ mentions the API endpoint on the API server that we're connecting to
__kind__ mentions the object type (in this case, Deployment)
__metadata__ we attach basic information to objects, like name
__spec__ we define the desired state of the deployment (e.g. at least 3 pods are running)
__labels__ are key-value pairs that can attach to any object
Once the object is created, Kubernetes attaches the __status__ field to the object.

### Labels

__Labels__ are key-value pairs that can attach to any object (e.g. a Pod).
They are used to organize and select a subset of objects (e.g. app:backend, app:frontend, env:qa, env:live)
You can select with:

__Equality-Based Selectors__ - allow filtering of objects based on Label keys and values
e.g. `=`, `==`, `!=` like `env==dev`

__Set-Based Selectors__ allow filtering of objects on a set of values
e.g. `in`, `notin`, `exist` operators like `env in (dev,qa)`

### ReplicationControllers

A __ReplicationController (rc)__ is a controller that is part of the master node's controller
manager. It makes sure that a specific number of replicas for a Pod are running at any time.
If there are extra, it kills the extra Pods. If there are less, it brings up more.

We use controllers like ReplicationController to create and manage Pods. ReplicationControllers only
support Equality-Based Selectors.

### ReplicaSets

A __ReplicaSet (rs)__ is the next-generation ReplicationController. It supports both equality and
set-based selection.

ReplicaSets can be used independently, but are mostly used by Deployments to orchestrate the Pod
creation, deletion, and updates. A Deployment automatically creates the ReplicaSets and we don't
have to worry about managing them.

### Deployments

A __Deployment__ object provides declarative updates to Pods and ReplicaSets. The
__DeploymentController__ is part of the master node's controller manager and it makes sure that the
current state always matches the desired state.

For example, we might have a Deployment that creates a ReplicaSet A. ReplicaSet A then creates
3 Pods. Each Pod uses the container `nginx:1.7.9` image.

#### Deployment Rollout

__Deployment Rollout__ is when we change our containers to use an image from `nginx:1.7.9` to
`nginx:1.9.1`, we update the Pods Template and a new ReplicaSet B gets created. Once ReplicaSet B
is ready, the Deployment starts pointing to it.

Note: A rollout is only triggered when we update the Pods Template for a deployment. Operations like
scaling the deployment do not trigger the deployment.

If something goes wrong, Deployments have a feature called __Deployment Recording__, which allows
you to rollback to a previously known state.

### Namespaces

We can partition the Kubernetes cluster into sub-clusters called __namespaces__. This is useful when
you have many users that you would like to organize into teams/projects. The names of the
resources/objects created inside a Namespace are unique, but not across Namespaces.

To see all namespaces, run:

	$kubectl get namespaces

Usually there are two default namespaces:

* __kube-sytsem__ - contains the objects created by the Kubernetes system
* __default__ - contains the objects which belong to any other Namespace, connects here by default
* __kube-public__ - a special namespace which is readable by all users and used for special purposes
  like bootstrapping a cluster

You can divide cluster resources within Namespaces using __Resource Quotas__.

### Daemonset

A Daemonset ensures that all (or some) nodes run a copy of a pod. As nodes are added or removed from the cluster,
a daemonset ensures that a pod gets added to the cluster.

Example uses include:

* Running a node monitoring daemon on every node
* Running a logs collection daemon on every node

An example of this is say you want a datadog-agent (to collect/send metrics) on every node.

```
kubectl -n mymonitoringnamespace get daemonsets
NAME      DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
datadog   8         8         8       8            8           kubernetes.io/os=linux   137d

# Describe in detail about a particular daemonset
kubectl -n mymonitoringnamespace describe daemonset datadog

# Do a rolling restart of a daemonset
kubectl -n mymonitoringnamespace rollout restart daemonset/datadog
```


## Authenitcation, Authorization, and Admission Control

To access and manage any resources/objects in the Kubernetes cluser, we need to access specific
API endpoints on the API server. Each access request goes through the following three stages:

* Authentication - Logs in a user
* Authorization - Authorizes the API requests added by the logged-in user
* Admission Control - Software modules that can modify or reject the requests based on some
  additional checks, like __Quota__

### Authentication

Kubernetes does not have an object called _user_, nor does it store _usernames_ or other related
details in its object store. Even without that, Kubernetes can use usernames for access control
and request logging.

#### Authentication Users

Kubernetes has two kinds of users:

* __Normal Users__ - managed outside of the Kubernetes cluster via independent services like
  User/Client Certifciates, a file listing usernames/passwords, Google accounts, etc.
* __Service Accounts__ - with Service Account users, in-cluser processes communicate with the
  API server to perform different operations. Most of the Service Account users are created
  automatically via the API server, but can also be created manually. The Service Account
  users are tied to a given Namespace and mount the respective credentials to communicate with
  the API server as Secrets.
* If properly configured, Kubernetes can also support __anonymouse requests__

#### Authentication Modules

For authenication, Kubernetes has different authenticator modules, including:

* __Client Certificates__ - to enable client certificate authentication, we need to
reference a file containing one or more certificate authorities by passing `--client-ca-file=FILE`
The certificate authorities mentioned in the file would validate the client certificates presented
to the API server
* __Static Token File__ - we can pass a file containing pre-defined bearer tokens with the
`--token-auth-file=FILE` option to the API server.
* __Bootstrap Token__ - used mainly for bootstrapping a new Kubernetes cluster
* __Static Password File__ is similar to a Static Token File, just with a file containing basic authentication
 details with `-basic-auth-file=FILE`
* __Service Account Tokens__ - automatically enabled authenticator that uses signed bearer tokens to verify
 the requests. These tokens are attached to Pods using the ServiceAccount Admission Controller, which
 allows in-cluster processes to talk to the API server
* __OpenID Connect Tokens__ - OpenID Connect connects with OAuth 2 providers to authenticate using external services
* __Webhook Token Authentication__ - Verification of bearer tokens are offloaded to a remote service
* __Keystone Password__ - can be enabled with `--experimental-keystone-url=<AuthURL>` option to the API server
* __Authenticating Proxy__ - use authenticating proxy if you want to program additional authentication logic

You can enable multiple authenticators with the first module to successfully authenticate the request to
short-circuit the evaluation. You should enable at least two methods: the service account tokens authenticator
and the user authenticator.

#### Authorization

After a successful authentication, users can send the API requests to perform different operations.
Then those API requests get authorized by Kubernetes using various authorization modules.

Authorization depends on API request __attributes__ like user, group, extra, Resource, and Namespace.
These attributes are compared against __policies__, ultimately saying if a request is allowed or not.

* __Node Authorizer__ - Node authorization is an authorization mode that specifically authorizes API requests made by Kubelets.


## ConfigMaps

A __ConfigMap__ is an API object used to store non-confidential data in key-value pairs.
Pods can consume ConfigMaps as environmental variables, command-line arguments, or as
configuration files in a volume.

A ConfigMap allows you to decouple environment-specific configuration from your container
images so that applications are more portable.

Kubernetes just creates a config map so you can access ENV variables on apps.
Any app that is deployed can access these variables!

https://github.com/thedevelopnik/kubernetes-intro/blob/master/pingpong-config.yml

	apiVersion: v1
	kind: ConfigMap
	metadata:
	  name: pingpong-config
	data:
	  PORT: "8000"

$kubectl create -f pingpong-config.yml
 configmap "pingpong-config" created

You can see configmaps with:

```
kubectl -n mynamespace get configmaps

kubectl -n mynamespace describe configmap pingpong-config
``

You can create a volume and volumemount your configuration to your application.
The actual application now has the ENV variables from our config map.

An example of this being used in real life is [Datadog's integrations](https://docs.datadoghq.com/containers/kubernetes/integrations/?tab=configmap) where you:

1. Create a configmap with the conf.d/<my_integration (e.g. amazon_msk.d)>/conf.yaml file
2. Volume and Volumemap the configmap to the pod

## Secrets

If you want to make a port secret (just as an example)

kubectl create secret generic port --from-literal=PORT=7000

Secrets are base64 encoded and can be encrypted at rest in etcd (default not)
Secrets limited to 1MB, kept in a temporary file system and only run on nodes that use the secret

```
kubectl -n mynamespace get secrets
# list of secrets
NAME                                    TYPE                                  DATA   AGE
my-secret                               Opaque                                  3     2d

kubectl -n mynamespace describe secret my-secrets
# shows you the type, namespace, labels, data
Name:         my-secrets
Namespace:    mynamespace
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
file_1:     1184 bytes
file_2:     1314 bytes
file_3:     1679 bytes


kubectl -n mynamespace get secret my-secret
NAME                     TYPE     DATA   AGE
my-secret                Opaque      3     2d

# Opaque type means it's the default secret type

```

### Secrets vs ConfigMaps

__ConfigMaps__ allow you to decouple configuration artifacts from image content to keep containerized
applications portable.

__Secrets__ are only accessible by this app or that app (not across apps), just strings (not much you can put in here,
e.g. half a mb). ConfigMaps can be accessed across apps and can hold giant json blobs.

ConfigMaps are more convenient. Can hold entire config files and JSON blobs (like redis configuration)
When secrets change, pods are not automatically updated. Allows you to rotate keys and by default cut
off access to potentially compromised pods. Pods get automatic updates when a ConfigMap changes.


# Deploying Kubernetes to Production

## Helm

__Helm__ is the package manager for Kubernetes. A __chart__ says here's the list of items you want,
here's the dependencies, etc.

Charts allow you to create, version, share, and publish.

## Steps

Creating on Google Cloud Platform:

1.) Create a Kubernetes Cluster
2.) Install Helm (package manger)
3.) Use Helm's Charts to install and deploy say Prometheus (monitoring), (nginx-ingress)
4.) We're using GitLab (GitLab Runner to connect to project's repo and execute CI/CD jobs)
5.) Applications appear in GitLab (e.g. you'll see Ingress to route requests to services, Prometheus
    as an open-source monitoring system, GitLab Runner to connect to repo and execute CI/CD jobs,
    Helm Tiller to streamline installing and managing Kubernetes applications; manages releases of your charts)
6.) You can view the automatically created yaml file from GitLab's Auto Devops

### Helm Commands

```
helm

❯ helm
The Kubernetes package manager

Common actions for Helm:

- helm search:    search for charts
- helm pull:      download a chart to your local directory to view
- helm install:   upload the chart to Kubernetes
- helm list:      list releases of charts

age:
  helm [command]

Available Commands:
  completion  generate autocompletion scripts for the specified shell
  create      create a new chart with the given name
  dependency  manage a chart's dependencies
  env         helm client environment information
  get         download extended information of a named release
  help        Help about any command
  history     fetch release history
  install     install a chart
  lint        examine a chart for possible issues
  list        list releases
  package     package a chart directory into a chart archive
  plugin      install, list, or uninstall Helm plugins
  pull        download a chart from a repository and (optionally) unpack it in local directory
  push        push a chart to remote
  registry    login to or logout from a registry
  repo        add, list, remove, update, and index chart repositories
  rollback    roll back a release to a previous revision
  search      search for a keyword in charts
  show        show information of a chart
  status      display the status of the named release
  template    locally render templates
  test        run tests for a release
  uninstall   uninstall a release
  upgrade     upgrade a release
  verify      verify that a chart at the given path has been signed and is valid
  version     print the client version information

Flags:
      --debug                       enable verbose output
  -h, --help                        help for helm
      --kube-apiserver string       the address and the port for the Kubernetes API server
      --kube-as-group stringArray   group to impersonate for the operation, this flag can be repeated to specify multiple groups.
      --kube-as-user string         username to impersonate for the operation
      --kube-ca-file string         the certificate authority file for the Kubernetes API server connection
      --kube-context string         name of the kubeconfig context to use
      --kube-token string           bearer token used for authentication
      --kubeconfig string           path to the kubeconfig file
  -n, --namespace string            namespace scope for this request
      --registry-config string      path to the registry config file (default "/Users/william.liu/Library/Preferences/helm/registry/config.json")
      --repository-cache string     path to the file containing cached repository indexes (default "/Users/william.liu/Library/Caches/helm/repository")
      --repository-config string    path to the file containing repository names and URLs (default "/Users/william.liu/Library/Preferences/helm/repositories.yaml")

Use "helm [command] --help" for more information about a command.
```

# Use helm template to locally render templates

```
helm template application -f application/values/staging/values.yaml --output-dir manifest
```

## GitLab's Auto DevOps

GitLab's Auto Devops automatically creates yaml files of setting up every project with a complete workflow
using some great boilerplate that has a lot of best practices.

Protop: Look at a CI/CD Pipeline and look at the context of the container pipeline.
We see 'Build', 'Test', 'Review', 'Dast' (security check), 'Performance' check, 'Cleanup'

## Kubernetes Cert Manager

There's a GitHub project called __cert-manager__ that will hit up letsencrypt for certificates.

## Ambassador

A Kubernetes API gateway that takes in say:

  Comes in with prefix of `/qotm`/ then send over to the service `qotm`

If you need things like __TLS__, just set TLS to True and then drop in your keys to `ambassador-certs`

## Istio

An open platform to connect, manage, and secure microservices; a __service mesh__.
Each application you deploy has an envoy sidecar; handles server traffic logging, routing, service discovery, loading
balancing, etc.

Lots of magic. Still very new. Mediates all inbound and outbound traffic for all services in the service mesh.

Downside is stil alpha and beta for everything.

## Environment Variables

Inject Environment Variables into your CI/CD system (e.g. GitLab), don't put things like passwords in your files.
Just call passwords to the Environment Variables

## CrashLoopBackOff

One of the things Kubernetes does is that if you take down a Pod and then bring it back up, but it crashes,
it'll stop at a certain amount (e.g. say after 5 crashes).

## Serverless

When creating bash scripts, check logs.
Creates AWS lambda functions, check logs from there.

## Kompose

Install Kubernetes Compose to turn a Docker-Compose to a Kubernetes


