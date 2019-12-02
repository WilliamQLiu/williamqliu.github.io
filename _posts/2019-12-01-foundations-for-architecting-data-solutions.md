---
layout: post
title: Foundations for Architecting Data Solutions
---


# {{ page.title }}

Notes for architecting data solutions

## Chapter 1 - Key Data Project Types and Considerations

The basis for any successful data project is a clear understanding of what you're tasked to build
and then understanding the major items that you need to consider in order to design a solid solution.

### Major Data Project Types

There are three data project types:

* __Data pipelines and data staging__ - think of Extract, Transform, and Load (ETL) type projects
  We collect, stage, store, and model our datasets so that we can do further analysis and data processing
* __Data processing and analysis__ - Create some actionable value, e.g. create reports or machine learning models
* __Applications__ - A data framework that supports live operational needs of applications (e.g. data backend for
  a web or mobile app)

### Things to consider

Some things to consider for every project (regardless of the project type):

* Primary Considerations - dependent on data project types, but examples might be latency and confirmations of delivery
* Risk management - guarantees around data delivery
* Team makeup - consider the types of skills, experience, and interests of everyone creating and using your data project
* Authentication - ensure that users accessing the system are who they claim to be
* Authorization - determine what data is allowed to be accessed by that user
* Encryption - protect against malicious users seeing data (data at rest and data on the wire)
* Auditing - capture activity related to data in your system (e.g. data lineage, who accessed what data)

#### Data Pipelines and Data Staging

