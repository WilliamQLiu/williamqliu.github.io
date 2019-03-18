---
layout: post
title: Terraform
---


# {{ page.title }}

## Summary

__Terraform__ is a tool for building, changing, and versioning infrastructure.
Terraform uses configuration files to describe the components of your
infrastrucute (e.g. compute instances, storage, networking, DNS entries, SaaS features).
Terraform generates an execution plan that describes what it will do to reach the desired
state of your infrastructure.

The idea is that Terraform describes your infrastructure as code (using a high-level configuration
syntax). This way the blueprint of your datacenter is versioned and is treated like code.

## Comparing Terraform

### Use Cases

Some example use cases with Terraform include:

* Setup required add-ons, configure DNS to set a CNAME, or setup Cloudflare as a CDN for a Heroku app
* N-tier architecture (e.g. 2-tier architecture is a pool of web servers that use a database tier).
  Each tier can be scaled independently and provide a separation of concerns. Terraform can manage
  these infrastructures, with each tier described as a collection of resources and the dependencies
  between each tier (e.g. database is available before the web servers)
* How to build and scale a service can be setup as a configuration (so teams can manage their own
  infrastructure instead of requiring a centralized operations team)
* Create a disposable environment (e.g. demo application using real infrastructure on AWS)
* Provision onto a scheduler (e.g. Borg, Mesos, YARN, Kubernetes) so that your infrastructure
  does not remain static, but can dynamically schedule Docker containers, Hadoop, Spark, etc.


### Terraform vs Configuration Management Tools

Configuration Management tools install and manage software on a machine that already exists.
Examples of configuration managemnt tools include Chef and Puppet. Terraform is not a 
configuration management tool so you might see it used in conjunction with tools like Chef.
Terraform focuses on provisioning while a tool like Chef focuses on setting up a resource
once it has been created.

### Terraform vs CloudFormation and Heat

There are tools like CloudFormation that allow the details of an infrastructure be described
in a configuration file. However, Terraform is cloud-agnostic and allows multiple providers
and services to be combined and composed.

Terraform also separates the planning phase from the execution phase by using an execution plan.
You run `terraform plan` to refresh the current state of an execution plan (using the configuration)
and an action plan is generated.
You can run `terraform graph` to visualize the plan, that way you can visualize dependent ordering.

### Terraform vs Boto and Fog

There are libraries like Boto and Fog that have native access to cloud providers and services
by using their APIs. Terraform does not give low-level programmatic access to providers, but
instead provides a high level syntax for describing how cloud resources and serviecs should
be created, provisioned, and combined.


