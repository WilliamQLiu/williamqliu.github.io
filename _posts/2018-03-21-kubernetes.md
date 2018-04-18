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
  On the Master Node, we have an API Server as well as a Scheduler and Controller
* One or more Worker Nodes
  On the Worker Node, we have a __kube-proxy__, a __Kublet__, and __pods__
* Distributed key-value store (__etcd__ is default), usually connected through Master Node

User interacts with the Master Node(s) through a CLI/API/Dashboard

## Master Node

The __master node__ is response for managing the Kubernetes cluster.
It is the entry point for all administrative tasks.

* User sommunicate to master node via API Server, CLI, or the GUI (Dashboard)
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

The __kube-proxy__ is the network proxy that runs on each worker node and that routes requests

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


## Components

### Pods

A logical set of containers
Like Docker-Compose Up
Scheduled on same host
Share network namespace
Mount same volumes
Can be labeled
ReplicaSet represents pod group (say I want 4 of these types of pods, is a ReplicaSet)

### Deployments

Deployments bind Replica Sets and Pod templates
E.g. I make a deployment of 3 nginx containers
 * Creates ReplicaSetA
I update the nginx version in the deployment
 * Creates ReplicaSetB and rolls over service

### Services

What you access (done via labels)
Group pods logically for access
This is what clients interact with
Created via Label Selection
app=api env=dev would create a service for all our api containers in the dev environment

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

### Minikube Dashboard

See a web gui dashboard with: `minikube dashboard`

## Kubectl

Can access through command line or through web gui

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

## Config Maps

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

The actual application now has the ENV variables from our config map

## Secrets

If you want to make a port secret (just as an example)

kubectl create secret generic port --from-literal=PORT=7000

Secrets are base64 encoded and can be encrypted at rest in etcd (default not)
Secrets limited to 1MB, kept in a temporary file system and only run on nodes that use the secret

### Secrets vs ConfigMaps

ConfigMaps are more convenient. Can hold entire config files and JSON blobs (like redis configuration)
When secrets change, pods are not automatically updated. Allows you to rotate keys and by default cut
off access to potentially compromised pods. Pods get automatic updates when a ConfigMap changes.

