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

### Install

```
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
terraform -install-autocomplete
```

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

https://www.terraform.io/docs/providers/

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

### Resources

A __resource__ is the most important element in Terraform. Each resource block describes one or more infrastructure
object (e.g. virtual network, compute instance, DNS record).

A resource block declares a resource of a given type (e.g. `aws_instance`) with a given local name.
We use this name to refer to this resource from somewhere else in the same Terraform module, but has no significance
outside of the scope of a module.

For a resource, you might see examples like the below, where we specify a __resource type__ and a __resource name__
(and the combination of these two parameters has to be unique). Each resource has to be associated with a
single resource type. Each resource type belongs to a __provider__ (which is a plugin for Terraform that offers a
collection of resource types).

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

So what does this all mean? We have a __provider__ like `AWS` that then has __resources__ like (`aws_vpc`)

#### Null Resources

You can setup a __Null Resource__ in order to perform local commands on our machine without having to deploy extra
resources. A null resource is often used with a `local-exec` provisioner.

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

See the current state with `terraform show` or by looking at your `terraform.tfstate` file.
An empty state file might look like:

    {
      "version": 3,
      "terraform_version": "0.11.13",
      "serial": 26,
      "lineage": "99c40ce9d-5f1f-0885-cc8aa8e7da96",
      "modules": [
        {
          "path": [
            "root"
          ],
          "outputs": {},
          "resources": {},
          "depends_on": []
        }
    }

Once you end up running a `terraform plan` and `terraform apply`, the resources are done deploying.
Your `terraform.tfstate` file should now be updated to have all of your resources. It might look like:

    ...
     "resources": {
        "docker_image.image_id": {
          "type": "docker_image",
          "depends_on": [],
          "primary": {
              "id": "sha256:.....ghost:latest",
              "attributes": {
                "id": "sha256:...ghost:latest",
                "name": "ghost:latest"
              },
            "meta": {},
            "tainted": false
          },
          "deposed": [],
          "provider": "provider.docker"
        }
    }
    ...

If the state file is deleted, then Terraform won't have anything to compare against.


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

#### local-exec Provisioner

The `local-exec` provisioner invokes a local executable after a resource is created. This invokes a process on
the machine running Terraform, not on the resource. You would use this with a null resource.

    resource "null_resource" "null_id" {
      provisioenr "local-exec" {
        command = "echo ${docker_container.container_id.name}:${docker_container.container_id.ip_address} >> container.txt"
      }
    }

#### remote-exec Provisioner

If you want to run a command on the resource, use the `remote-exec` provisioner.

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

A variable can be setup with:

    variable "image_id" {
      type = string
    }

Or if you want to specify the type and a default value

    variable "availability_zone_names" {
      type    = list(string)
      default = ["us-west-1a"]
    }

__Default__

The keyword `default` can be used, meaning if no value is set when calling the module, the default argument.
The default argument requires a literal value and cannot reference other objects in the configuration.

__Type__

You can have a __type constraint__, meaning the value has to be of type:

* `list(<TYPE>)`
* `set(<TYPE>)`
* `map(<TYPE>)`
* `object({<ATTR_NAME> = <TYPE>, ... })`
* `tuple([<TYPE>, ...])`

#### Accessing Variables

Your configurations can use items prefixed with `var` to access a variable.

    provider "aws" {
      region = var.region
    }

You can also assign variables as command-line flags

    terraform apply -var 'region=us-east-2'

If you want a custom variable file, you can call it `terraform.tfvars` or `*.auto.tfvars` in the current directory.
Terraform will automatically load these files to populate variables. If the file is called something else, you can
use the `-var-file` flag on the command line to specify a file (or multiple files).

#### Environment Variables In Terraform

Terraform can read __Environment Variables__ in the form of `TF_VAR_name` to find the value for a variable.
For example, `TF_VAR_region` can be set to the `region` variable. Or we can run `export TF_VAR_env=dev`
to set the `env` variable as `dev`. To unset it, run `unset TF_VAR_env`

Commands:

    Use environment variable with `export TF_VAR_myvariable=myvalue`
    Remove environment variable with `unset TF_VAR_myvariable`

### Lists

You can define lists like:

    # implicitly by using brackets [...]
    variable "cidrs" { default = [] }

    # explicitly
    variable "cidrs" { type = list }

Or you can specify lists in your `terraform.tfvars` file like:

    cidrs = [ "10.0.0.0/16", "10.1.0.0/16" ]

### Maps

You can use maps to create lookup tables. These are just key/value pairs.

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

Example:

Say we want to add a variable 'env' that can be dev or prod

    # variables
    variable "env" {
      description = "env: dev or prod"
      default     = "dev"
    }

    variable "image_name" {
      type        = "map"
      description = "Image for container"
      default = {
        dev  = "ghost:latest"
        prod = "ghost:alpine"
      }
    }

    variable "container_name" {
      type        = "map"
      description = "Name of the container"
      default     = {
        dev  = "blog_dev"
        prod = "blog_prod"
      }
    }

    # resources
    resource "docker_image" "image_id" {
      name = "${lookup(var.image_name, var.env)}"
    }

So you basically use `${lookup(var.my_variable)}` to use the variables
You can then run with `terraform plan -out=tfdev_plan -var env=dev`

### Expressions

Terraforms uses __expressions__ to refer to or compute values within a configuration. This is super important because
we need a way to reference other values that we have in a variable file, a data source or a module.

We can have simple expressions like `"hello"` or `5` or `var.my_variable`.
We can also have complex expressiones such as data exported by resources, conditional evaluation, and built-in functions.

Expressions can reference to named values:

* `<RESOURCE_TYPE>.<NAME>` is an object representing a managed resource of the given type and name.
  The attributes of the resource can be accessed using dot or square bracket notation.
* `var.<NAME>` for value of an input variable
* `local.<NAME>` is the value of the local value
* `module.<MODULE_NAME>.<OUTPUT_NAME>` is the output value from a child module called by the current module
* `data.<DATA_TYPE>.<NAME>` is an object representing a data resource of the given data source type and name
* `path.module` is the filesystem path of the module where the expression is placed
* `path.root` is the filesystem path of the root module of the configuration
* `path.cwd` is the filesystem path of the current working directory. In normal use, this is same as `path.root`
* `terraform.workspace` is the name of the currently selected workspace

If the resource has the `count` argument set, the value is a list of objects representing its instances.

More here: https://www.terraform.io/docs/configuration/expressions.html#references-to-named-values

### Interpolation Syntax

You can reference variables a variety of ways. For example, on variables:

    `${var.foo}` will interpolate the `foo` variable value

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

### Local Values

A __local value__ assigns a name to an expression, allowing it to be used multiple times within a module
without repeating it. Think of local values as comparable to a traditional programming language as a function's
local temporary symbols. Really, you'll only be using local values if you're referencing the same thing over
and over.

  locals {
    service_name = "forum"
    owner        = "Community Team"
  }

  locals {
    instance_ids = concat(aws_instance.blue.*id, aws_instance.green.*.id)
  }

  locals {
    # Common tags to be assigned to all resources
    common_tags = {
      Service = local.service_name
      Owner   = local.owner
    }
  }

You can reference local values from elsewhere in the module with an expression like `local.common_tags`

    resource "aws_instance" "example" {
      # ...
      tags = local.common_tags
    }

So why use locals? One neat feature from locals is that if you need to use a `data` resource, you can
use this in locals, but cannot use that in a variable.

### Outputs

You can define an output to show us specific information. Here, we create an output variable `ip` and the `value`
field specifies what the value will be (usually dynamically generated). For the below example, we're taking the
`public_ip` attribute of the elastic IP address and saving that to the output variable `ip`, where it can be
used as say input for something else.

    output "ip" {
      value = aws_eip.ip.public_ip
    }

You can view outputs with `terraform output` (and more specifically `terraform output ip` for specific variables)

### Inputs

Input variables serve as parameters for a Terraform file. A variable block configures a single input variable
for a Terraform module. Each block declares a single variable.

    variable [NAME] {
      [OPTION] = "[VALUE]"
    }

Within the body block (between the `{}` is the configuration for the variable, which can accept the following arguments:

* `type` (Optional): if set, this defines the type of the variable. Valid values are `string`, `list`, and `map`
* `default` (Optional): if set, the default value for a variable. If no default is provided and used, will raise an error
* `description` (Optional): a human-friendly description for the variable

You can also apply variables (to override the default) during an `apply`, like `terraform apply -var 'foo-bar'`.

Example:

    #Define variables
    variable "container_name" {
      description = "My container name"
      default     = "my blog"
    }

    variable "image_name" {
      description = "Image for container"
      default     = "ghost:latest"
    }

    # Download the latest Ghost Image
    resource "docker_image" "image_id" {
      name = "${var.image_name}"
    }

    # Start the Container
    resource "docker_container" "container_id" {
      name  = "${var.container_name}"
      image = "${docker_image.image_id.latest}"
      ports {
        internal = "${var.int_port}"
        external = "${var.ext_port}"
      }
    }

    # Output the IP Address of the Container
    output "IP Address" {
      value       = "${docker_container.container_id.ip_address}"
      description = "The IP for the container"
    }

    output "container_name" {
      value       = "${docker_container.container_id.name}"
      description = "The name of the container"
    }

### Breaking out Variables and Outputs

You can split out your files so that we have separate outputs and variables

main.tf
outputs.tf
variables.tf

### Terraform Workspaces

Terraform workspaces can help you deploy multiple environments. By using workspaces, we can deploy multiple
environments simultaneously without the state files colliding. Commands are:

* `workspace` along with subcommands of `new`, `list`, `select`, and `delete`, `show`
* `terraform workspace show` will show the name of the current workspace
* `terraform workspace list` will list all workspaces (e.g. dev, prod)
* `terraform workspace select dev` will select a specific workspace (e.g. dev)
* `terraform workspace new dev` to create a `dev` workspace or `terraform workspace new prod` to create a `prod` workspace

This is how we can have a dev environment and a production environment at the same time. This changes our state files
so that we have:

    $ ls
    terraform.tfstate
    terraform.tfstate.d
    terraform.tfstate.backup

    $ terraform workspace new dev
    $ terraform workspace new prod

    $ ls terraform.tfstate.d
    dev  prod

    ls terraform.tfstate.d/prod/
    terraform.tfstate

    ls terraform.tfstate.d/dev/
    terraform.tfstate

    $ terraform workspace select dev

## Modules

 __Modules__ in Terraform are self-contained packages of Terraform configurations that are managed as a group.
Without modules, you need to edit Terraform configurations directly. This works, but has issues with lack of
organization, lack of reusability, and difficulties in management for teams.

We already setup a 'root module' where we have the following:

    $ ls ~/terraform/myterraform/

    # root module
    main.tf
    variables.tf
    outputs.tf

    $ mkdir -p modules/image && touch main.tf variables.tf outputs.tf
    $ mkdir -p modules/container && touch main.tf variables.tf outputs.tf

    # image module
    main.tf
    variables.tf
    outputs.tf

    # container module
    main.tf
    variables.tf
    outputs.tf

### Module Structure

Reusable modules are defined using all of the same configuration language concepts we use in root modules.
We have:

* Input variables to accept values from the calling module (think of them like function arguments)
* Output values to return results to the calling module, which it can then use to populate arguments elsewhere
  (think of them as function return values)
* Local values (think of them as local temporary symbols)
* Resources to define one or more infrastructure objects that the module will manage

### Building out a Module

We can create our image module. An example might be:

    # main.tf
    # Download the Image
    resource "docker_image" "image_id" {
      name = "${var.image_name}"
    }

    # variables.tf
    variable "image_name" {
      description = "Name of the image"
    }

    # outputs.tf
    output "image_out" {
      value = "${docker_image.image_id.latest}"
    }

We are able to get the output value through interpolation syntax

Run a `terraform init`, `terraform plan`, and `terraform apply`

Note: When you run a `terraform plan`, sometimes you will see a message like:

```
Note: Objects have changed outside of Terraform

Terraform detected the following changes made outside of Terraform since the last "terraform apply":
```
These are just changes that are an FYI (does not affect your current plan).

### Using a module

In our 'root' module's `main.tf`, we can use other modules like so:

    # main.tf
    module "image" {
      source     = "./image"
      image_name = "${var.image_name}"
    }

    # assuming we have a container module
    module "container" {
      source   = "./container"
      image    = "${module.image.image_out}"
      container_name = "${var.container_name}"
      int_port = "${var.int_port}"
      ext_port = "${var.ext_port}"
    }

Variables are found through interpolation syntax (e.g. `"${var.int_port}"` looks for the variable `int_port`

* We need to specify the `source` (path to where the files are).
* Notice how we use the `module.image.image_out`, which is the output from that module (from `outputs.tf`)

### Calling a Child Module

To call a module means to include the contents of that module into the configuration with specific values
for its __input variables__.

You can specify the source of a module a few different ways.
https://www.terraform.io/docs/modules/sources.html

Local Path

    module "my-module" {
      source = "../../modules/parent-module"
    }

GitHub

    module = "my-module" {
      source = "github.com/hashicorp/example"
    }

S3 Buckets

    module = "my-module" {
      source = "s3::https://s3...../myfile.zip"
    }

### When to use a module

Over-using modules can make your overall Terraform configuration harder to understand and maintain.
A good module should raise the level of abstraction by describing a new concept in your architecture that is
constructed from resource types offered by providers

It is not recommended to write a module that is just a thin wrapper around a single other resource type.
If you have trouble finding a name for your module that isn't the same as the main resource type inside it,
that may be a sign that your module is not creating any abstraction and so the module is adding unnecessary
complexity. If that is the case, then use the resource type directly in the calling module instead.

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

### Data Source

A data source is accessed via a special resource known as a __data resource__ using a `data` block.

    data "aws_ami" "web" {
      most_recent = true

      owners = ["self"]
      tags = {
        Name   = "app-server"
        Tested = "true"
      }

      filter {
        name   = "tag:Component"
        values = ["web"]
    }

So how do you reference this?

Follow the format of `data.<resource_name>.<alias>`, (e.g. `data.aws_ami.web.id`)

E.g.

    resource "aws_instance" "web" {
      ami           = data.aws_ami.web.id
      instance_type = "t1.micro"
    }

www.terraform.io/docs/configuration/data-sources.html `

Depending on the resource, you can have different arguments

#### Data Source (and Providers) vs Resource

A __data source__ allows data to be fetched or computed for use elsewhere in a Terraform configuration.
Use of data sources allow a Terraform configuration to build on information defined outside of Terraform,
or defined by another separate Terraform configuration. Data sources are read-only views into pre-existing data
or they compute new values on the fly within Terraform itself.

A __provider__ is responsible for defining and implementing data sources.

A __resource__ causes Terraform to create and manage a new infrastructure component. Resources can be things like a
a low level component (e.g. physical server, a virtual machine, or a container) or a higher level component (e.g.
email provider, DNS record, database provider)

## Terraform Remote State

In order to manage your infrastructure, Terraform needs to store state. When run locally, we store state in a file
called `terraform.tfstate` and this state is used to map your real world infrastructure to your configuration.
This local state file is referenced when creating a plan, then once your plan is applied and the real world
infrastructure is actually updated, terraform also updates your state file. Prior to any operations, Terraform
does a refresh to update the state with the real infrastructure (through `terraform refresh` command)

The issue with a local state file is that in a team environment, you might have multiple people editing the file.
The solution is that we can store this state file remotely in a place like S3 in a bucket.

    # export your aws access key, aws secret access key, aws default region

    # terraform.tf
    terraform {
      backend "s3" {
        key = "terraform/terraform.tfstate"
      }
    }

    terraform init --backend-config "bucket=[BUCKET_NAME]"  # initializes the S3 backend
    terraform validate

## AWS Terraform Example

If we wanted to setup an example AWS infrastructure with Terraform, we would have the following pieces:

AWS

* terraform_s3_bucket with our remote state files
* tf_internet_gateway
* tf_internet_gateway interfaces with a VPC
* In the VPC, we have our public route table `tf_public_rt` (e.g. 172.16.0.0, 172.16.1.0, 172.16.2.0)
* In the VPC, we have our private route table `tf_private_rt` (e.g. 172.16.0.0, 172.16.1.0, 172.16.2.0)
* In the VPC, we have Availability Zones 1 and 2 (AZ1, AZ2)
* Each AZ has a VPC subnet
* There is a security group that encompasses all of the servers (across AZs)
* Then we have our servers (e.g. `tf_server1`, `tf_server2`)

Notes:
* route tables have a set of rules (aka __routes__) that determine where network traffic is routed
* each __subnet__ in your VPC must be associated with a route table; the table controls the routing for the subnet
* a subnet can only be associated with one route table at a time, but you can associate multiple subnets with the same route table

## Debugging

Turn on debug when you need to do troubleshooting:

    TF_LOG=DEBUG terraform <command>

## import

If you have existing infrastructure, use the `import` command (e.g. run with `terraform import` to add infra
to Terraform's state file).

## if-else statement

You can't really do an if-statement directly; instead you have to work around it.

Booleans can be used in a Terraform tenerary operation to create an if-else statement like:

    CONDITION ? TRUEVAL : FALSEVAL

If you have multiple if-else statements, consider using the above to assign variables if a statement is true,
then do a `coaelsce` to get the first value that is not empty.

## Terraform Best Practices

github.com/ozbillwang/terraform-best-practices

* Avoid hard coding resources (e.g. `account_number=123456`)
* Minimum AWS permissions necessary for a Terraform run
* Define `type` for each variable, otherwise you will get weird error messages (e.g. `variable "region" { default = "us-east-2"  type = "string" }`)
* Isolate environments (create resources with different names for each environment and each resource)
* Retrieve state meta data from a remote backend (via `terraform_remote_state`)

## Real Life Scenarios


### Error Acquiring the State Lock

When: There's a lock, e.g. your CI is running terraform plan and so are you manually.

```
│ Error: Error acquiring the state lock
│
│ Error message: ConditionalCheckFailedException: The conditional request failed
│ Lock Info:
│   ID:        89e18eba-e0b1-4f2d-6452-xxxxxxxx
│   Path:      mylocation/terraform-states/my-file-staging.json
│   Operation: OperationTypeApply
│   Who:       william.liu
│   Version:   1.0.0
│   Created:   2022-08-10 18:25:00.05326249 +0000 UTC
│   Info:
```

Solution: `terraform force-unlock 89e18eba-e0b1-4f2d-6452-xxxxxxxx` to remove the lock. Double check the Who is
what you're expecting.

### What's going on with my state

When: What's going on with my state?
Solution: `terraform state list`

```
data.terraform_remote_state.something
data.terraform_remote_state.somethingelse
data.terraform_remote_state.subnets
aws_route53_record.some_route
module.mymodule.aws_instance.instance[0]
```

Look more closely with:

```
terraform state show <item>
terraform refresh

# Get the state again in case it is out of sync
terraform state rm $THE_MODULE_NAME_FROM_OUTPUT
terraform import $THE_MODULE_NAME_FROM_OUTPUT $IDENTIFIER
```

### Terraform Target

When: I don't want to run my whole plan
Solution: Target resources:

```
terraform plan -target="<resource_1>" -target="<resource_2>" -out=plan

terraform apply -target=<resource_1>
e.g. terraform apply -target=aws_iam_role.my_role

# Destroy a single object (with target)
terraform destroy --target=module.docker-app.module.docker-app.module.ec2-instance.aws_instance.instance[0]
```

### Terraform Taint

When: Something looks wrong with an object, let's rebuild it
Solution: Recreate with taint; Note: watch the plan carefully (e.g. if a destroy and create or an update, etc)

```
terraform init
terraform taint --module=docker-app.docker-worker.ec2-instance aws_instance.instance
terraform plan -target module.docker-app.module.docker-worker.module.ec2-instance.aws_instance.instance -module-depth=1 -out=plan
terraform apply "plan"

terraform untaint <resource> # if you want to untaint it
```

### Terraform fmt

When: Validation errors
Solution: `terraform fmt myfile.tf` your files

### Terraform init fails

When: `terraform init fails`
Solution: `terraform init -reconfigure`

```
Initializing the backend...
╷
│ Error: Backend configuration changed
│
│ A change in the backend configuration has been detected, which may require migrating existing state.
│
│ If you wish to attempt automatic migration of the state, use "terraform init -migrate-state".
│ If you wish to store the current configuration with no changes to the state, use "terraform init -reconfigure".
╵
```

### Best Practices

[Google Best Practices for Terraform](https://cloud.google.com/docs/terraform/best-practices-for-terraform)
