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

### Configure List

If you're not sure what credentials are being used, you can run a:

    aws configure list

      Name                    Value             Type    Location
      ----                    -----             ----    --------
    profile                <not set>             None    None
    access_key     ****************YYYY shared-credentials-file
    secret_key     ****************XXXX shared-credentials-file
    region                us-west-2              env    AWS_DEFAULT_REGION

## Boto 3 Stubs

Note: Need MyPy installed first (for static typing)
Add annotations for boto3 packages - https://pypi.org/project/boto3-stubs/

## AWS Auto Completer

Identify where auto_completer is:

    $which aws_completer
    /usr/bin/aws_completer

Then add this to your `.bashrc`

Now you can 'Tab' through your commands

## Example Cli usage

    $aws ec2 describe-regions --output table
    ----------------------------------------------------------
    |                     DescribeRegions                    |
    +--------------------------------------------------------+
    ||                        Regions                       ||
    |+-----------------------------------+------------------+|
    ||             Endpoint              |   RegionName     ||
    |+-----------------------------------+------------------+|
    ||  ec2.ap-south-1.amazonaws.com     |  ap-south-1      ||
    ||  ec2.eu-west-3.amazonaws.com      |  eu-west-3       ||
    ||  ec2.eu-north-1.amazonaws.com     |  eu-north-1      ||
    ||  ec2.eu-west-2.amazonaws.com      |  eu-west-2       ||
    ||  ec2.eu-west-1.amazonaws.com      |  eu-west-1       ||
    ||  ec2.ap-northeast-2.amazonaws.com |  ap-northeast-2  ||
    ||  ec2.ap-northeast-1.amazonaws.com |  ap-northeast-1  ||
    ||  ec2.sa-east-1.amazonaws.com      |  sa-east-1       ||
    ||  ec2.ca-central-1.amazonaws.com   |  ca-central-1    ||
    ||  ec2.ap-southeast-1.amazonaws.com |  ap-southeast-1  ||
    ||  ec2.ap-southeast-2.amazonaws.com |  ap-southeast-2  ||
    ||  ec2.eu-central-1.amazonaws.com   |  eu-central-1    ||
    ||  ec2.us-east-1.amazonaws.com      |  us-east-1       ||
    ||  ec2.us-east-2.amazonaws.com      |  us-east-2       ||
    ||  ec2.us-west-1.amazonaws.com      |  us-west-1       ||
    ||  ec2.us-west-2.amazonaws.com      |  us-west-2       ||
    |+-----------------------------------+------------------+|


## S3

Using the AWS CLI, you can just type in `aws s3 ls` and you'll see what S3 buckets
your account has access to.

### View all files and directories in a bucket

    aws s3 ls mybucket
    2018-08-17 14:34:18         10 my_test_file.txt

    aws s3 ls mybucket --recursive # see multiple files and below

### Cat fileds

You can't `cat` or view files normally in the command line, but you can copy them to the terminal with a `-`

    aws s3 cp s3://mybucket/myfile -

    # e.g.
    aws s3 cp s3://aws-athena-query-results-335934781941-us-east-1/Unsaved/2019/01/31/cdb040a6-fb0c-4c31-bcbf-f390b42b2f8c.csv -
    "request_timestamp","elb_name","request_ip","request_port","backend_ip","backend_port","request_processing_time","backend_processing_time","client_response_time","elb_response_code","backend_response_code","received_bytes","sent_bytes","request_verb","url","protocol","user_agent","ssl_cipher","ssl_protocol"
    "2015-01-07T04:00:01.206255Z","elb_demo_005","245.85.197.169","8222","172.46.214.105","8888","0.001163","0.001233","1.21E-4","200","200","0","705","GET","http://www.example.com/images/858","HTTP/1.1","""Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/602.1.50 (KHTML, like Gecko) Version/10.0 Safari/602.1.50""","-","-"
    "2015-01-07T04:00:01.612598Z","elb_demo_003","251.165.102.100","24615","172.41.185.247","80","8.68E-4","0.001232","5.27E-4","200","200","0","572","GET","https://www.example.com/images/905","HTTP/1.1","""Mozilla/5.0 (X11; CrOS x86_64 8172.45.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.64 Safari/537.36""","DHE-RSA-AES128-SHA","TLSv1.2"
    "2015-01-07T04:00:02.793335Z","elb_demo_007","250.120.176.53","24251","172.55.212.88","80","8.7E-4","0.001561","0.001009","200","200","0","2040","GET","http://www.example.com/articles/518","HTTP/1.1","""Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/42.0.2311.135 Safari/537.36 Edge/12.246""","-","-"
    "2015-01-07T04:00:03.068897Z","elb_demo_003","240.243.137.214","31120","172.45.116.34","443","8.26E-4","5.35E-4","2.29E-4","500","500","0","1617","GET","https://www.example.com/images/254","HTTP/1.1","""Mozilla/5.0 (X11; CrOS x86_64 8172.45.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.64 Safari/537.36""","DHE-RSA-AES128-SHA","TLSv1.2"
    "2015-01-07T04:00:03.470121Z","elb_demo_007","244.167.215.143","32484","172.53.89.104","443","2.09E-4","0.001394","0.001415","200","200","0","4834","GET","https://www.example.com/images/113","HTTP/1.1","""Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.111 Safari/537.36""","DHE-RSA-AES128-SHA","TLSv1.2"
    "2015-01-07T04:00:04.159502Z","elb_demo_001","240.57.230.67","4784","172.50.222.158","80","6.68E-4","8.07E-4","5.45E-4","200","200","0","115","GET","https://www.example.com/jobs/910","HTTP/1.1","""Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/602.1.50 (KHTML, like Gecko) Version/10.0 Safari/602.1.50""","DHE-RSA-AES128-SHA","TLSv1.2"
    "2015-01-07T04:00:04.778187Z","elb_demo_001","242.202.189.146","6250","172.52.188.181","8888","0.001972","1.89E-4","0.001207","200","200","0","232","GET","http://www.example.com/jobs/85","HTTP/1.1","""Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/602.1.50 (KHTML, like Gecko) Version/10.0 Safari/602.1.50""","-","-"
    "2015-01-07T04:00:06.178798Z","elb_demo_005","253.204.166.199","13508","172.34.137.103","8888","0.001051","1.85E-4","0.001612","200","200","0","140","GET","http://www.example.com/images/348","HTTP/1.1","""Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/602.1.50 (KHTML, like Gecko) Version/10.0 Safari/602.1.50""","-","-"
    "2015-01-07T04:00:06.607063Z","elb_demo_004","247.65.202.70","20205","172.31.105.109","80","0.001375","9.79E-4","9.67E-4","200","200","0","3693","GET","http://www.example.com/jobs/709","HTTP/1.1","""Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_2) AppleWebKit/601.3.9 (KHTML, like Gecko) Version/9.0.2 Safari/601.3.9""","-","-"
    "2015-01-07T04:00:06.625672Z","elb_demo_005","242.65.92.41","22840","172.52.141.90","443","0.001378","0.001168","0.001142","200","200","0","4582","GET","http://www.example.com/jobs/943","HTTP/1.1","""Mozilla/5.0 (X11; CrOS x86_64 8172.45.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.64 Safari/537.36""","-","-"

### Get file size

aws s3 ls s3://mybucket/ -- recursive | awk 'BEGIN {total=0}{total+=$3}END{print total/1024/1024" MB"}'

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


