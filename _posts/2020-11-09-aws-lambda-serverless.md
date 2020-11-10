---
layout: post
title: AWS Lambda (Serverless Services)
---


# {{ page.title }}

AWS Lambda is a way to run code without provisioning or managing servers (aka __serverless services__).

## Basics

You create a __function__ in the programming language of your choice. An example function might look like:

    import json

    def lambda_handler(event, context):
        print("Will Liu Test")

        return {
            'statusCode': 200,
            'body': json.dumps('Hello from Lambda!')
        }

You can test out the function by clicking on 'Test'. Results would look like:

    Response:
    {
      "statusCode": 200,
      "body": "\"Hello from Lambda!\""
    }

    Request ID:
    "16aeeb91-51c4-4b4b-8cbf-f56f9e0f39b4"

    Function logs:
    START RequestId: 16aeeb91-51c4-4b4b-8cbf-f56f9e0f39b4 Version: $LATEST
    Will Liu Test
    END RequestId: 16aeeb91-51c4-4b4b-8cbf-f56f9e0f39b4
    REPORT RequestId: 16aeeb91-51c4-4b4b-8cbf-f56f9e0f39b4    Duration: 0.99 ms    Billed Duration: 100 ms    Memory Size: 128 MB    Max Memory Used: 51 MB

## Sources and Sinks

You can add a __trigger__ as a __source__ (i.e. what starts your lambda function?)
You can add a __destination__ as a __sink__ (i.e. where do your want the results of your lambda function to go when they fail or succeed?)

### Sources

* S3 - a specific bucket (e.g. with this prefix) and action (e.g. PUT, POST)
* MSK - a cluster and topic
* CloudWatch Logs
* DynamoDB table
* SNS
* SQS
* Datadog (through Amazon EventBridge)

### Lambda Layers

You can configure your lambda function to pull in additional code and content as __layers__.
A layer is a ZIP archive that contains libraries, a custom runtime, or other dependencies.

### Real-Time Stream Processing Example

Example of a real time stream processing pipeline that reads data from a kafka topic and writes, runs a lambda,
then writes out to an S3 location

1. Real-time event data is sent to an MSK cluster under a specific Topic.
   You can read the topic either at `TRIM_HORIZON` (i.e. read all the available messages) or
   at the `LATEST` (i.e. skip previous messages with possible data loss)
2. Setup your lambda to process some code (e.g. remove PII)
3. Setup a destination (e.g. S3 bucket)


---

# Amazon EventBridge

__Amazon EventBridge__ is a serverless event bus service that lets you connect your applications with data
from a variety of sources. EventBridge delivers a stream of real-time data from your own applications
and routes that data to targets like AWS lambda.

You can setup __event buses__ that receive events from a variety of sources and match them to rules.
You can enable __event discovery__ on an event bus to generate __EventBridge Schemas__ for events on that bus.
You create a __rule__ that watches for certain events, then routes them to AWS targets you choose.

An example event pattern might look like this if you have __CloudTrail__ enabled for __S3__.

    {
      "source": [
        "aws.s3"
      ],
      "detail-type": [
        "AWS API Call via CloudTrail"
      ],
      "detail": {
        "eventSource": [
          "s3.amazonaws.com"
        ],
        "eventName": [
          "PutObject"
        ],
        "requestParameters": {
          "bucketName": [
            "testing"
          ]
        }
      }
    }

## Schema Registry

Search, find, and track different event schemas using __schema registry__. You can setup using:

* AWS Schema - e.g. S3
* Custom Schema - custom create from a template or discover from JSON

You can pick say an AWS event schema registry that shows the schema for say S3.

# Amazon Kinesis

__Data Streams__ can have __producers__ that put records into a Data Stream.
You can use an Amazon Kinesis Agent or an AWS SDK for producing messages. Data Streams
can also have __consumers__ (e.g. Amazon Kinesis Data Firehose, Amazon Kinesis Data Analytics) for taking your data
from a source (e.g. CloudWatch Logs, CloudWatch Events), processing the records (apply an AWS Lambda function, convert
the record format to Parquet), then choose a sink (S3, Redshift, an HTTP endpoint, third-party service provider like Datadog).

## Amazon Kinesis Data Firehose

An __Amazon Kinesis Data Firehose__ is a __consumer__ that can load streaming data into data lakes, data stores,
and analaytics services. It can capture, transform, and deliver streaming data to S3, Redshift, HTTP endpoints, and
service providers like Datadog.

