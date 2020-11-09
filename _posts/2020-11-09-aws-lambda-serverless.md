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
*


### Real-Time Stream Processing Example

Real-time event data is sent to an MSK cluster under a specific Topic.
You can read the topic either at `TRIM_HORIZON` (i.e. read all the available messages) or `LATEST` (i.e. skip previous messages with possible data loss)


