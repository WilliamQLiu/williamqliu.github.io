---
layout: post
title: Kubernetes
---


# {{ page.title }}

# Installation

Install Docker, Minikube, kubectl and VirtualBox

# CNCF

__CNCF__ (cncf.io) is a sub foundation of the Linux Foundation
Kubernetes is the 'anchor' project
Check out Linux Foundation's course on edx for Kubernetes (4 hours)
Local Kubernetes via Minikube are some basic deployments

# Kubernetes

## What is it

Container Orchestration

Similar to:

* Docker Swarm
* Mesos Marathon
* AWS ECS
* Hashicorp Nomad

## Why?

Kubernetes is the whole package instead of doing one piece and needing other pieces (e.g. Ansible + Ansible Vault for secrets)

Automate container deployment across instances
Schedule deployments
Manage inter-container communication
Group/organize containers

## Features

See it has it all, even key-value storage for env variables

Binpacking
Self-healing
Scaling
Service Discovery/Load Balancing
Automated Rollouts/backs
Secrets/Config management
Storage

## General Architecture

Master Node (can have multiple masters for high availablity)
Master Node has a key-value store (etcd is default)
Master Node links to Worker Nodes

## Master Node

Communicate via API Server
Scheduler interacts with workers
Controller Manager manages state of cluster

## Worker Nodes

Container runtime (default is Docker, can be rkt)
A __Pod__ is say a group of Apps together; say you hook up storage (shared among pod)
Proxy routes requests
Kubelet manages pods within runtime

## Componetns

### Pods

A logical set of containers
Like Docker-Compose Up
Logical set of containers
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

