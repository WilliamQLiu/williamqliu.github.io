---
layout: post
title: AWS Fargate
---


# {{ page.title }}


AWS Fargate is an abstraction that lets you use Amazon ECS to run containers without having
to manage servers or clusters of EC2 instances. When you run ECS tasks and services,
you package your application in containers, specify the OS, CPU and memory requirements,
define networking and IAM policies, and launch the application. Each Fargate task has its own
isolation boundary and does not share the underlying resources with another task.

## Components

### Clusters

An Amazon ECS __cluster__ is a logical grouping of tasks or services.
You can use a cluster to isolate your applications.

### Task Definitions

A __task definition__ is a text file that describes one or more containers that form your application.
It's a JSON doc that you can use to describe up to 10 containers and serves as the blueprint for
your application (e.g. what OS, which containers to use, which ports to open for your app, what data volumes).
Your entire application doesn't need to be on a single task definition.

### Tasks

A __task__ is the instantiation of a task definition within a cluster. After you create a task definition
for your application, you can specify the number of tasks to run on your cluster.
Tasks can be run as standalone or as part of a service.

### Services

A __service__ runs and maintains your desired number of tasks. If a task fails or stops, the ECS service
scheduler launches another instance based on your task definition.
