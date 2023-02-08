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

```
	apiVersion: v1
	kind: ConfigMap
	metadata:
	  name: pingpong-config
	data:
	  PORT: "8000"
```

You can then run the following to create the configmap:

```
$kubectl create -f pingpong-config.yml
configmap "pingpong-config" created
```

You can see configmaps with:

```
kubectl -n mynamespace get configmaps

kubectl -n mynamespace describe configmap pingpong-config
```

You can create a volume and volumemount your configuration to your application.
The actual application now has the ENV variables from our config map.

An example of this being used in real life is [Datadog's integrations](https://docs.datadoghq.com/containers/kubernetes/integrations/?tab=configmap) where you:

1. Create a configmap with the conf.d/<my_integration (e.g. amazon_msk.d)>/conf.yaml file
2. Volume and Volumemap the configmap to the pod


### Updating ConfigMap

If you delete a configmap, it'll come back up again quickly (few seconds).

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

## Deploying Configmaps

After you update your configmap, make sure to restart your deployment
so that the new configmap gets picked up, e.g.

```
❯ kubectl -n my-namespace get deployments
NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
my-deployment 					2/2     2            2           17h

kubectl rollout restart -n my-namespace deployment/<deploymentName>
```


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

## kubectl commands

List of basic [kubectl commands](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)

* Create a resource: `kubectl create -f FILENAME`, `kubectl create <resource, e.g. role, service, deployment, configmap, job>`
* Get a resource, prints out info about that resource: `kubectl get <resource>`
* Create and run an image in a pod: `kubectl run NAME --image=image`
* Expose a resource as a new Kubernetes service `kubectl expose -f FILENAME`

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


## kubectl completion

Add this to the end of your .zshrc

```
source <(kubectl completion zsh)
```

## kubectl config

Lets you see the current context, set the cluster.

```
kubectl config current-context
kubectl config get-clusters
kubectl config get-users
```

## kubectl explain

```
kubectl explain <resource>
kubectl explain pods
```

## kubectl (commands for working with apps)

### kubectl top

Get resource usage

```
kubectl top pod

kubectl top node
```

### kubectl debug

```
kubectl debug <resource>
kubectl debug -n my-namespace debug my-pod --image alpine
```

### kubectl events

```
❯ kubectl -n my-namespace get events
LAST SEEN   TYPE     REASON    OBJECT                                     MESSAGE
98s         Normal   Pulling   pod/my-pod-6c499895dd-q56ld   Pulling image "alpine"
96s         Normal   Pulled    pod/my-pod-6c499895dd-q56ld   Successfully pulled image "alpine" in 2.400820168s
96s         Normal   Created   pod/my-pod-6c499895dd-q56ld   Created container debugger-s2jd4
```

### Replacing a resource

```
kubectl -n my-namespace get <resource> -o yaml > my_resource.yaml
cat my_resource.yaml # to verify
kubectl apply -f my_resource.yaml
```

## Issue/Background: How do you isolate applications?

Software components running on the same machine will require different, often conflicting
versions of libraries. We can solve this issue by using virtual machines and containers
to isolate environments.


## Virtual Machines vs Containers

What is a virtual machine? __Virtual Machines__ (VMs) is when applications are isolated with their own guest operating system. This means a VM is used when there are a few applications (e.g. App A, App B) running on the same environment (i.e. on a Guest OS). If you have more applications that are small, then giving each application their own VM is a waste of hardware. The main benefit of VMs is that they have full isolation (each VM
has its own Linux kernel).

So what does this look like? Say you have three VMs (VM1, VM2, VM3) and each VM is running two Apps,
each on a Guest OS of their own. All three of the VMs would be running underneath on a bare-metal machine that has its own (single) Host OS and Hypervisor.

### Hypervisors

The **Hypervisor** divides the physical hardware resources into smaller sets of virtual resources that
can be used by the operating system inside each VM. The applications running inside the VMs can do
system calls to the guest OS' kernel in the VM, and then the kernel then performs x86 instructions
on the virtual CPU, which are sent through the hypervisor to the host's physical CPU.

There are two types of hypervisors:

* hypervisors that do not use a host OS
* hypervisors that do use a host OS

## Containers

What is a container? **Containers** have all system calls run on the same sinngle kernel that are then run on the host physical CPU.
Use containers when you have a large number of processes that you want to isolate since containers have
very little overhead (e.g. does not need to run a virtual OS so it does not have the overhead of
needing to run a VM set of system services, only the host OS's set of system services). Since you do
not need to run a VM OS, you also do not have a bootup like a VM; the process in a container starts up
immediately.

### Linux Namespaces and Linux Control Groups (cgroups)

So how does a container work? The processes are isolated, but how does it do that if everything is running
on the same operating system? The answer is Linux Namespaces and Linux Control Groups (cgroups).

* **Linux Namespaces** ensure each process sees its own personal view of the system (file, processes, network interfaces, hostname, etc).
* **Linux Control Groups (groups)** limit the amount of resources the process can consume (CPU, memory, network bandwidth, etc)


Linux Namespaces - By default, each Linux system initially has one single namespace.
All system resources (e.g. filesystems, process IDs, user IDs, network interfaces) belong to this
single namespace. You can create additional namespaces. Other kinds of namespaces exist like:

* Mount (mnt)
* Process ID (pid)
* Network (net)
* Inter-process communication (ipc)
* UTS
* User ID (user)

Each namespace kind is used to isolate a certain group of resources. For example, the UTS namespace
determines what hostname and domain name the process running inside that namespaces sees. A process
might be assigned a specific namespace (e.g. thinks its seeing X hostname and domain name) while
another process might be assigned another namespace (e.g. think its seeing Y hostname and domain name).

Linux Control Groups (cgroups) - cgroups are a Linux kernel feature that limits the resource usage of
a process (or a group of processes).

### Docker container platform

**Docker** is a container system that makes containers easy to move across different machines.
You package up the application, its libraries and other dependencies and even the whole OS file system
into a simple package that can be used on any other machine also running Docker.
We basically get a lot of the same isolation levels that VMs do. Docker is made up of:

* **Images** - You package your application and its environment into an image. This has the filesystem
  that will be available to the application and the path to the executable when the image is run.
  Images are usually built on layers (e.g. a layer might be an ubuntu image, a python image)
* **Registries** - a Docker Registry is a repo for your Docker images; like GitHub, but for images
  instead of code. You can share (push and pull) other images out there.
* **Containers** - a Docker based container is a regular Linux container created from a docker-based
  container image. A running container is a process that runs on the host running Docker, but is
  completely isolated from both the host and all other processes running on it.

### rkt container platform

**rkt** is another Linux container engine (similar to Docker). The Open Container Initiative (OCI)
created a container image format that rkt follows. Kubernetes can also run rkt (not just Docker).

## Introducing Kubernetes

**Kubernetes** is a software system that allows you to easily deploy and manage containerized applications on top of it.
Kubernetes enables you to run your software applications on thousands of computer nodes as if all those
nodes were a single giant computer.

Use case: a developer submits a list of apps to the Kubernetes master node, then Kubernetes
deploys them to the cluster of worker nodes (does not matter where it lands). The developer can specify
that certain apps must run together and Kubernetes will deploy them on the same worker node.

Think of Kubernetes as an operating system for the cluster. k8s provides things like service discovery,
scaling, load-balancing, self-healing, and leader election. Application developers can focus on
implementing actual features instead of figuring out how to integrate them with the infrastructure.

### Architecture of a k8s cluster

A k8s cluster is composed of two types of nodes:

* the **master node**, which hosts the **Kubernetes Control Plane** that controls and manages the whole
  k8s system. The Control Plane is what controls the cluster and makes it function; this includes the
  **Kubernetes API Server**, the **Scheduler**, and the **Controller Manager**, and **etcd**
* **worker nodes** that run the actual applications you deploy; this includes a container runtime like
  Docker or rkt to run your containers, the **Kubelet**, and the **Kubernetes Service Proxy (kube-proxy)**

So how do you run an application in Kubernetes?

1. Package the application up into one or more container images, push those images to an image registry
2. Post a description of your app to the **Kubernetes API Server**; the description includes information like the container image that has your application or its components, how those components are related to each other, and which ones need to be run co-located (together on the same node).
3. When the **Kubernetes API server** processes your application's description, the **Scheduler** schedules the specified groups of containers onto the available worker nodes on computational resources required by each group.
4. The **Kubelet** on those nodes then instructs the container runtime (e.g. Docker) to pull the required container images and run the container
