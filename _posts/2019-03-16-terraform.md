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

__Execution Plans__

* Show the intent of the deploy
* Can help ensure everything in development is intentional

__Resource Graph__

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

We can declare resources in `.tf.json` and `.tf` formats (e.g. say `main.tf`).
When you run terraform, verify that there are no other `*.tf` files in your directory because
Terraform loads all of them. Also, never hard code in credentials to these files.

### Terraform Steps

The steps for terraform are:

* `terraform init` - run for a new configuration, which initializes local settings and data that gets used by subsequent commands
  This downloads and installs any provider binary for the providers in use (e.g. an aws provider)
* `terraform validate` -
* `terraform plan` - see the execution plan before applying it
* `terraform apply` - run in the same directory as your `my_example.tf`, which will show the __execution plan__

### Terraform Step Output

The output is similar to git diff, where:

* A `+` next to the resource means it will be created
* A `-` next to a resource means it will be destroyed
* A `-/+` means Terraform will destroy and recreate the resource rather than updating it in-place.
* A `~` means update in place
* A `(known after apply)` means it will know the value after the resource is created.

### Terraform State Data

Terraform writes data into a `terraform.tfstate` file. This state file is really important.
It keeps track of the IDs of created resources so that Terraform knows what it is managing.
This file has to be saved and distributed to anyone who might run Terraform.

See the current state with `terraform show`

### Terraform Destroy

Resources can be destroyed using the `terraform destroy` command, which is like `terraform apply`, but behaves
as if all of the resources have been removed from the configuration.

### Implicit and Explicit Dependencies

Terraform can automatically infer when one resource depends on another.
For example, the reference to an `aws_instance.example.id` creates an __implicit dependency__ on the `aws_instance`
You should use an implicit dependency whenever possible.
The `depends_on` is used when there are dependencies between resources that are NOT visible to Terraform.
Resources that do not depend on other resources can be created in parallel with other resources.

### Provisioners

__Provisioners__ are only run when a resource is __created__ or __destroyed__.
Provisioners are not a replacement for configuration management and changing the software of an already-running server.
Think of them as a way to bootstrap your server on first creation or to do some cleanup task.
They might move some certs around or run an actual configuration management tool (e.g. `knife <command>`) on creation
or getting some data before a destroy operation.

#### Failed Provisioners and Tainted Resources

If a resource successfully creatse but fails during provisioning, Terraform will error and mark the resource as __tainted__.
A tainted resource means it has been created, but might not be safe to use. Terraform will not attempt to restart
provisioning on the same resource because it isn't guaranteed to be safe. Instead, Terraform will remove any
tainted resources and create new resources, attempting to provision them again after creation.

## Hashicorp Configuration Language

Syntax:

* Single line comment starts with `#`
* Multi-line comments are wrapped with `/*` and `*/`
* Values are assigned with the syntax `key = value`
* Strings are in double-quotes
* Strings can interpolate other values using syntax wrapped in `${}`, e.g. `${var.foo}`
* Numbers are assumed to be base 10
* Boolean values are: `true`, `false`
* Lists of primitive types can be made with square brackets `[]`, e.g. `["foo", "bar"]`
* Maps can be made with braces `{}` and colons `:`, e.g. `{"foo": "bar", "bar": "baz}`

Indent two spaces for each nestling level
With multiple arguments, align their equals signs


### Variables

Your configurations can use items prefixed with `var` to create a variable.

    provider "aws" {
      region = var.region
    }

You can also assign variables as command-line flags

    terraform apply -var 'region=us-east-2'

If you want a custom variable file, you can call it `terraform.tfvars` or `*.auto.tfvars` in the current directory.
Terraform will automatically load these files to populate variables. If the file is called something else, you can
use the `-var-file` flag on the command line to specify a file (or multiple files).

Terraform can read __Environment Variables__ in the form of `TF_VAR_name` to find the value for a variable.
For example, `TF_VAR_region` can be set to the `region` variable.

### Lists

You can define lists like:

    # implicitly by using brackets [...]
    variable "cidrs" { default = [] }

    # explicitly
    variable "cidrs" { type = list }

Or you can specify lists in your `terraform.tfvars` file like:

    cidrs = [ "10.0.0.0/16", "10.1.0.0/16" ]

### Maps

You can use maps to create lookup tables.

    variable "amis" {
      type = "map"
      default = {
        "us-east-1" = "ami-b374d5a5"
        "us-west-2" = "ami-4b32be2b"
      }
    }

You can then use maps like:

    resource "aws_instance" "examle" {
      ami = var.amis[var.region]
      instance_type = "t2.micro"
    }

This means that `[var.region]` references `var.amis`. You can also use `var.amis["us-east-1"]` to get back `ami-b374d5a5`

### Expressions

Terraforms uses __expressions__ to refer to or compute values within a configuration.
We can have simple expressions like `"hello"` or `5`.
We can also have complex expressiones such as data exported by resources, conditional evaluation, and built-in functions.

### Terraform Console

You can run `terraform console` to help evaluate expressions

    $terraform console
    > docker_container.container_id.name
    ghost_blog
    > docker_container.container_id.ip_address
    172.17.0.2
    > 1 + 5
    6

Ctrl + C will exit out of your console

### Outputs

You can define an output to show us specific information. Here, we create an output variable `ip` and the `value`
field specifies what the value will be (usually dynamically generated). For the below example, we're taking the
`public_ip` attribute of the elastic IP address and saving that to the output variable `ip`, where it can be
used as say input for something else.

    output "ip" {
      value = aws_eip.ip.public_ip
    }

You can view outputs with `terraform output` (and more specifically `terraform output ip` for specific variables)

## Modules

Without modules, you need to edit Terraform configurations directly. This works, but has issues with lack of
organization, lack of reusability, and difficulties in management for teams. __Modules__ in Terraform are
self-contained packages of Terraform configurations that are managed as a group.

### Terraform Registry

The __Terraform Registry__ has a directory of ready-to-use modules.

## Terraform Commands

### Tainting Resources

`taint` manually marks a resource for recreation.

    terraform taint [RESOURCE_NAME]
    e.g. terraform taint docker_container.container_id
    terraform plan  # see what will be changed

### Untainting Resources

`untaint` manually unmarks a resource as tainted.

    terraform untaint [RESOURCE_NAME]
    e.g. terraform untaint docker_container.container_id
    terraform plan  # see what will be changed

