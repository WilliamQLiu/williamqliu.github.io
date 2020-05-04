---
layout: post
title: AWS Elastic File Systems (EFS)
---


# {{ page.title }}

Amazon __Elastic File System__ (EFS) is fully managed NFS file system (Network File System) for use with AWS Cloud Services.

If you have a Linux based system, EFS creates a file system that is made available to Amazon VPCs.
Your EC2s can mount your file system to store and access data.
Elastic means that your system can grow or shrink to your needs.

### When to use EFS

If you need to scale performance for any workload, automatically scale your file system storage up or down,
have tigher security (e.g. control access with IAM or VPC, need to encrypt your data at rest and/or in transit)

### When not to use EFS

There are a lot of different options for storage out there, including:

* Amazon Elastic Block Store (EBS) - more like a traditional file system
  (easy point in time snapshots, good for testing and development)
* S3 - Easy to use, good for data lakes and big data analytics

## Storage Classes

AWS EFS has two storage classes:

* Standard storage
* Infrequent storage (EFS IA) for files not accessed every-day

You can enable 'EFS Lifecycle Management' on your file system and files not accessed according to the lifecycle
policy you choose are automatically moved to EFS IA (to save cost).

## AWS Elastic File System Encryption

For file systems, there are two forms of encryption:

* encryption of data in transit
* encryption of data at rest

You might want to ask yourself:

* What are you encrypting? Not everything needs to be encrypted.
* When are you encrypting the data? (e.g. data at rest, data in transit, both?)

If you need both, AWS recommends creating an encrypted file system mounting your file system using encryption of data in transit.

### Encrypting Data at Rest

You can create encrypted file systems through the AWS Console or CLI just like you would an unencrypted file system.
You can monitor whether encryption at rest is being used for Amazon EFS file systems by using __CloudWatch__ and __CloudTrail__
to check for the creation of a file system and verify that encryption is enabled.

* EFS uses a single customer master key (the AWS managed CMK for Amazon EFS or a custom CMK) to encrypt and decrypt file system metadata.

#### How Encryption At Rest Works

In an encrypted file system, data and metadata are automatically encrypted BEFORE being written to the file system.
When data and metadata is read, it is automatically decrypted before being presented to the application. With Amazon EFS,
this is handled automatically do you don't have to do anything.

* Choose the CMK used to encrypt and decrypt file data (file contents)

## EFS

You create your EFS File System. To access the EFS file system in a VPC, you need __mount targets__.

* You can create one mount target in each Availability Zone.
* If the VPC has multiple subnets in an AZ, you can only create a mount target in
  only one of those subnets.

## Mounts

You can use `/etc/fstab` to mount EFS automatically when the Amazon EC2 instance reboots.
The command `mount -a` (run during system start up) mounts the file systems listed in `/etc/fstab`
The mount helper is part of the `amazon-efs-utils` set of tools.

To see mounts, you can type in `mount`

You can create one mount target in each Availability Zone.
