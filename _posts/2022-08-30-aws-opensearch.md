---
layout: post
title: AWS OpenSearch Service
---


# {{ page.title }}

AWS OpenSearch Service (aka Elasticsearch Service) is a managed service that makes it easy to deploy, operate,
and scale OpenSearch clusters in the AWS Cloud.

## Pieces

1. Create an OpenSearch Service Domain (i.e. an OpenSearch cluster)
2. Upload data for indexing


### Domain

An OpenSearch Service **Domain** is basically an OpenSearch cluster.
Domains are clusters with the settings, instance types, instance counts, and storage resources that you specify.

After setting up a Domain, you'll get an endpoint that looks like:

https://search-my-domain.us-east-1.es.amazonaws.com

### Upload Data to OpenSearch for indexing

You can add a single document to the `movies` domain with curl:

```
curl -XPUT -u 'master-user:master-user-password' 'domain-endpoint/movies/_doc/1' -d '{"director": "Burton, Tim", "genre": ["Comedy","Sci-Fi"], "year": 1996, "actor": ["Jack Nicholson","Pierce Brosnan","Sarah Jessica Parker"], "title": "Mars Attacks!"}' -H 'Content-Type: application/json'
```

https://docs.aws.amazon.com/opensearch-service/latest/developerguide/indexing.html




