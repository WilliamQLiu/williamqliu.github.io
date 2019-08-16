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

### Open Source vs Enterprise

Terraform is available as open source and as Enterprise.
Enterprise provides advanced collaboration and governance

### Features

### Comparing Terraform

Terraform

__Infrastructure as Code (IaC)__:

* Idempotent
* High-level syntax
* Easily reusable with modules

Execution Plans

* Show the intent of the deploy
* Can help ensure everything in development is intentional

Resource Graph

* Illustrates all changes and dependencies

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
in a configuration file. However, Terraform is __cloud-agnostic__ and allows multiple providers (e.g AWS, Google Cloud)
and services to be combined and composed. Other vendors like CloudFormation are vendor-locked and only support
one vendor.

Terraform also separates the planning phase from the execution phase by using an execution plan.

* You run `terraform plan` to refresh the current state of an execution plan (using the configuration)
and an action plan is generated.
* You can run `terraform graph` to visualize the plan, that way you can visualize dependent ordering.

### Terraform vs Boto and Fog

There are libraries like Boto and Fog that have native access to cloud providers and services
by using their APIs. Terraform does not give low-level programmatic access to providers, but
instead provides a high level syntax for describing how cloud resources and serviecs should
be created, provisioned, and combined.

## Terraform Commands

https://www.terraform.io/docs/commands/index.html

Common commands:

    apply: Builds or changes infrastructure
    console: Interactive console for Terraform interpolations
    destroy: Destroys Terraform-managed infrastructure
    fmt: Rewrites configuration files to canonical format
    get: Downloads and installs modules for the configuration
    graph: Creates a visual graph of Terraform resources
    import: Imports existing infrastructure into Terraform
    init: Initializes a new or existing Terraform configuration
    output: Reads an output from a state file
    plan: Generates and shows an execution plan
    providers: Prints a tree of the providers used in the configuration
    push: Uploads this Terraform module to Terraform Enterprise to run
    refresh: Updates local state file against real resources
    show: Inspects Terraform state or plan
    taint: Manually marks a resource for recreation
    untaint: Manually unmarks a resource as tainted
    validate: Validates the Terraform files
    version: Prints the Terraform version
    workspace: Workspace management

### Terraform File

Command Line:

    mkdir -p terraform/basics
    vi main.tf


    main.tf
    # Download the latest Ghost image
    resource "docker_image" "image_id" {
      name = "ghost:latest"
    }

    terraform init  # initializes Terraform, goes and installs the provider we're using (e.g. Docker)
    terraform validate  # validate the Terraform file to make sure no errors, no msg is good

### Providers

__Providers__ are APIs that Terraform uses to interact with.

ihttps://www.terraform.io/docs/providers/

Example Providers include:

* Google Cloud Platform
* AWS
* Azure
* Docker
* GitHub

You can see your list of providers in your project with `ls .terraform/plugins/linux_amd64/` (e.g. `provider.docker`)

#### Docker Provider and Resources

Let us look at a specific provider, the Docker Provider. You can see the list of __Resources__ including:

* `docker_container`
* `docker_image`
* `docker_network`
* `docker_volume`

This can be found in the documentation under providers.

#### Resources

For a resource, you might see examples like the below, where we specify a __resource type__ and a __resource name__
(and the combination of these two parameters has to be unique).

    resource "docker_image" "image_id" { ... }
    resource "docker_container" "container_id" { ... }

For example:

    # Download the latest Ghost image
    resource "docker_image" "image_id" {
      name = "ghost:latest"
    }

    # Start the Container
    resource "docker_container" "container_id" {
      name  = "ghost_blog"
      image = "${docker_image.image_id.latest}"
      ports {
        internal = "2368"
        external = "80"
      }
    }

We can declare resources in `.tf.json` and `.tf` formats (e.g. say `main.tf`)

### Terraform Steps

The steps for terraform are:

* `terraform validate`
* `terraform plan`
* `terraform apply`

### Expressions

Terraforms uses __expressions__ to refer to or compute values within a configuration.
We can have simple expressions like `"hello"` or `5`.
We can also have complex expressiones such as data exported by resources, conditional evaluation, and built-in functions.

