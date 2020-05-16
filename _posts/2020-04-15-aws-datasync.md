---
layout: post
title: AWS DataSync
---


# {{ page.title }}

__AWS Datasync__ allows you to transfer data between AWS storage and on-premise resources.
You can use this to move data from say one EFS to another EFS or say from EFS to S3.

## What are some examples

Use cases include:

* Data migration - move active datasets into S3 or EFS.
* Move data for cloud processing - Move data into or out of AWS for processing (e.g. your company has a
  hybrid setup where there are some on-premise servers and AWS resources)
* Data archiving - Move cold data from expensive storage systems to longer term storage (e.g. to S3 Glacier)

Depending on your use case, we might:

* transfer from on-premise to AWS
* transfer from AWS NFS file system to another AWS NFS file system
* from S3 to AWS NFS

## How it works

Steps are to:

1. Deploy a DataSync agent
2. Create a task by specifying the location of your data source and destination
3. Start the Transfer

### Agent

What's an Agent then? An agent is a virtual machine used to read data from or write data to a location
An Agent that is operating properly has the status ONLINE.

