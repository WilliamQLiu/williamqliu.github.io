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


