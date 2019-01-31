---
layout: post
title: AWS CLI and Boto
---


# {{ page.title }}

Notes about AWS Cli and Boto3

## Install

https://docs.aws.amazon.com/cli/latest/userguide/installing.html

Install w/ Ubuntu:

    sudo apt-get install awscli

Install w/ Python:

    pip install awscli --user

You should be able to see:

    aws --version
    aws-cli/1.11.13 Python/3.5.2 Linux/4.15.0-30-generic botocore/1.4.70

If you can't run the `aws` command, then make sure the following location is in your PATH

    PATH=$PATH:/home/idx/.local/bin/
    /home/<username>/.local/bin/aws

## Configure

Setup AWS with

    aws configure

You'll see the following configuration files in `~/.aws/`:

    ls ~/.aws/
    config  credentials

## AWS Auto Completer

Identify where auto_completer is:

    $which aws_completer
    /usr/bin/aws_completer

Then add this to your `.bashrc`

Now you can 'Tab' through your commands

## S3

Using the AWS CLI, you can just type in `aws s3 ls` and you'll see what S3 buckets
your account has access to.

### View all files and directories in a bucket

    aws s3 ls mybucket
    2018-08-17 14:34:18         10 my_test_file.txt

### Copy files

    # Copy to an s3 bucket
    aws s3 cp my_file s3://mybucket

    # Copy from an s3 bucket
    aws s3 cp s3://mybucket myfile

    # Copy recursively (to and from) an s3 bucket
    aws s3 cp my_ s3://mybucket --recursive

### Remove Files

    aws s3 rm s3://mybucket/myfile
    aws s3 rm s3://mybucket/mydir/ --recursive

### Sync Files

Sync files locally over to s3

    aws s3 sync . s3://mybucket/path

Sync files from s3 bucket to local

    aws s3 sync s3://mybucket/path .

Sync local files with deletion

    aws s3 sync . s3://mybucket/path --delete

## S3API

Use the s3api API under:
https://docs.aws.amazon.com/cli/latest/reference/s3api/index.html#cli-aws-s3api

### Create bucket

    aws s3api create-bucket --bucket my-bucket --region us-west-1 --create-bucket-configuration LocationConstraint=us-west-1

### View Bucket Policy

    aws s3api get-bucket-policy --bucket my-bucket

### Apply a Bucket Policy to another Bucket

    aws s3api get-bucket-policy --bucket mybucket1 --query Policy --output text > policy.json

    aws s3api put-bucket-policy --bucket mybucket2 --policy file://policy.json


