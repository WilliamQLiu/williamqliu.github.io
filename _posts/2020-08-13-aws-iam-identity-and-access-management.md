---
layout: post
title: AWS Identity and Access Management
---


# {{ page.title }}


AWS __Identity and Access Management (IAM)__ is a service that helps you control access to AWS resources.
You use IAM to control who is authenticated (signed in) and authorized (has permissions) to use resources.

## Terms

### Root User

When you first create an AWS account, you have a single sign-in identity that has complete access to all
AWS services and resources (aka the __root user__). You do not want to use the root user for everyday tasks.
Instead, use the root user to create an IAM user.

### Principal

The __principal__ is a person or application that can make a request for an action/operation on an AWS resource.

### Authentication

The principal has to be __authenticated__ (signed into AWS) using their credentials to send a request to AWS.
Examples include:

* console authentication - sign in with email address and password
* IAM user - provide your account ID or alias along with your user name and password

Additional steps like multi-factor authentication (MFA) may be needed or added

### Authorization

__Authorization__ allows or blocks your request to complete an action on a resource.
The request checks for policies that apply to the request. Most policies are stored as JSON documents
and specify the permissions for principal entities. You can apply policies a few different ways:

* Use __identity-based policies__ to provide your users with permissions to access the AWS resources in their own account
* Use __resource-based policies__ to provide cross-account access (but watch out, this is much more advanced)

### Actions or Operations

Each resource has certain actions you can take on it. E.g. you can view, create, edit, or delete a resource like
an EC2 instance. In order for you to run an action/operation on a resource, you need the necessary actions in
a policy that applies to your logged in person (aka __Principal__).

### Resources

A resource is an object that exists within a service. Examples include Amazon EC2 instances, an IAM user, an
S3 bucket, etc. You run an action/operation on each resource. E.g. you can request to delete an IAM role or list S3 buckets.

## Identities

__IAM identities__ are used to provide authentication for people and processes in your AWS account.
We have break this up a few ways:

* __IAM Users__ - an IAM user is a person or service that uses the IAM user to interact with AWS
  An example of this is to give people the ability to sign into the AWS management console.
  A user in AWS has a name, password and up to two access keys that can be used in the API or CLI.
  You make the IAM user a member of a group (that has the appropriate permission policies attached - recommended)
  OR you can directly attach policies to the user (not recommended).
* __IAM Groups__ - an IAM group is a collection of IAM users. E.g. you can have an `Admin` group that has admin
  privileges. You attach policies to principles.
* __IAM Roles__ - an IAM role is like a user in that it is an identity with permission policies that determine
  what the identity can and cannot do in AWS. A role just does not have any credentials (password or access keys)
  associated with it. Instead of being uniquely associated with one person, a role is intended to be assumed by
  anyone who needs it.

### IAM Users vs IAM Roles

Use an IAM User when:

* You created an AWS account and you are the only person who works in your account
* Other people in your group need to work in AWS and your group is using no other identity mechanism

Use an IAM Role when:

* An application (e.g. EC2 instance) makes requests to other AWS resources.
* An app that runs on a mobile phone needs to make requests to AWS

### Federated Roles

Your users might already have an identity outside of AWS (e.g. your corporate directory or a SAML like OKTA).
If these users need to work with AWS resources, then they also need AWS security credentials.
You can use an IAM role to specify permissions for those users whose identity is __federated__ from your organization
or a third party identity provider.

When do you use this? We might create an AWS IAM role for engineers with a `Principal` of `Federated` through the
SAML provider OKTA. The action would be to `sts:AssumeRoleWithSAML`.

## Terraform

### IAM Resources

Terraform __resources__ describe an infrastructure object, such as an EC2 or DNS record.

    resource "aws_instance" "web" {
      ami           = "ami-a1b2c3d4"
      instance_type = "t2.micro"
    }

You can access information about resources in the same module using __expressions__.
Use the syntax `<RESOURCE_TYPE>.<NAME>.<ATTRIBUTE>` syntax to reference a resource attribute in an expression.

### IAM Data Sources

Terraform __data sources__ allow data to be fetched or computed for use somewhere else in the Terraform configuration.
You would use a data source when your Terraform needs to read information defined outside of Terraform or
in another separate Terraform configuration.

You access a data source via a special resource known as a __data resource__, which is declared using a `data` block.

    # Fetch information about a specific IAM policy
    data "aws_iam_policy" "example" {
      arn = "arn:aws:iam::XXXXXX:policy/UsersManageOwnCredentials"
    }

    # Creates an IAM policy document in JSON format, for use with resources like an `aws_iam_policy` resource
    data "aws_iam_policy_document" "example" {
      statement {
        sid = "1"

        actions = [
          "s3:ListAllMyBuckets",
          "s3:GetBucketLocation",
        ]

        resources = [
          "arn:aws:s3:::*",
        ]
      }

      statement {
        actions = [
          "s3:ListBucket",
        ]

        resources = [
          "arn:aws:s3:::${var.s3_bucket_name}",
        ]

        condition {
          test     = "StringLike"
          variable = "s3:prefix"

          values = [
            "",
            "home/",
            "home/&{aws:username}/",
          ]
        }
      }

      statement {
        actions = [
          "s3:*",
        ]

        resources = [
          "arn:aws:s3:::${var.s3_bucket_name}/home/&{aws:username}",
          "arn:aws:s3:::${var.s3_bucket_name}/home/&{aws:username}/*",
        ]
      }
    }

    resource "aws_iam_policy" "example" {
      name   = "example_policy"
      path   = "/"
      policy = data.aws_iam_policy_document.example.json
    }
