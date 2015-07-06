---
layout: post
title: Architecting Highly Available Applications on AWS
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
*  [Highly Available vs Fault Tolerant](#haft)
    -  [Highly Available]
    -  [Number of Nines]
    -  [Fault Tolerance]
*  [AWS Architecture](#awsarchitecture)
    -  [Chaos Monkey](#chaosmonkey)
    -  [AWS S3](#s3)
    -  [AWS CloudFront](#cloudfront)
    -  [AWS Route 53](route53)
*  [AWS Lab 1](#lab1)
    -  [How to manually fix a bad instance](#lab1a)
    -  [How to automatically fix bad instances](#lab1b)
*  [High Availability Cost](#hacost)
    -  [Auto Scaling Components](#autoscalecomponents)
*  [AWS Lab 2 - Create High Availability on Web Tier](#lab2)
    -  [Load Balancers](#lab2a)
    -  [Auto Scaling Part 1](#lab2b)
    -  [Auto Scaling Part 2](#lab2c)
    -  [Summary: High Availability on Web Tier](#lab2d)
*  [AWS Storage Options](#storage)
    -  [AWS Elastic Block Store (EBS)](#ebs)
    -  [AWS EC2 Instance Storage](#instancestorage)
    -  [AWS S3 Storage](#s3storage)
    -  [AWS Storage Options Summary](#storagesummary)
*  [AWS Database Options](#dbarchitecture)
    -  [Do It Yourself RDBMS](#diyrdbms)
    -  [Database Storage Considerations](#dbstorageconsiderations)
    -  [Caching](#caching)
    -  [Database Generic Replication](#dbgenericreplication)
    -  [Database Sharding](#sharding)
    -  [NoSQL Databases](#dbnosql)
    -  [AWS RDS](#rds)
    -  [AWS DynamoDB](#dynamodb)
    -  [DB Summary](#dbsummary)
*  [AWS Lab 3 - Create High Availability on Database Tier](#lab3)
    -  [AWS RDS to create highly available relational database](#lab3a)
    -  [AWS DynamoDB (NoSQL) to store session state](#lab3b)
    -  [Lab Summary: Use AWS RDS or DynamoDB](#lab3c)
*  [High Availability Design Patterns](#hadesignpatterns)
    -  [Common Design Patterns](#commondesignpatterns)
    -  [VPN and AWS Direct Connect](#vpndirectconnect)
    -  [Building Loosely Coupled Systems](#looselycoupled)
*  [AWS Lab 4 - Making outbound traffic highly available using NAT instances](#lab4)

##<a id="summary">Summary</a>

These are notes from attending the NYC AWS Training and Certification for Highly Available Applications using Amazon Web Services.

##<a id="haft">Highly Available vs Fault Tolerant</a>

__Highly Available__ means removing single points of failure (because "Everything fails all the time")

*  Assume everything fails and work backwards
*  Avoid single points of failure (e.g. if database fails, can still write)
*  Implement elasticity (e.g. even if not failing, might be really slow, can add to speed up)
*  Design loosely coupled architectures
*  Goal is to have no downtime, always available
*  _Availability_ defined as percentage of time an application operates during its work cycle
*  Loss of availability is known as an outage or downtime (e.g. app is offline, app is slow, either planned or unplanned)

__Number of Nines__ in Levels of Availability
*  1 Nine = 90% uptime, downtime per year = 36.5 days
*  2 Nines = 99%, downtime per year = 3.65 days
*  3 Nines = 99.9%, downtime per year = 8.76 hrs (probably the standard, we have two load balancers, two web servers, two application servers, two databases, etc.)
*  4 Nines = 99.99%, downtime per year = 52.6 min (at this point, to go from 3 Nines to 4 Nines, you need to put servers in another building / some distance, but this adds in some latency issues)
*  5 Nines, etc... (things like S3 has 9 Nines)

__Fault Tolerance__ means built-in redundancy so apps can continue functioning when components fail
*  Ability of a application to accommodate growth without changing design
*  Be able to recover from failure

##<a id="awsarchitecture">AWS Architecture</a>

AWS encourages everything in the cloud as 'off-site' and 'multi-site'.
*  Lots of great diagrams showing big picture of architecture here: http://aws.amazon.com/architecture/
*  Single or Multi-Regions?  Depends on how much money you have/want to spend and what your app does without say a key component like a database.  Recommend to a single default region + multiple Availability Zones
*  AWS is literally a programmable data center, automation is one of its key uses.  Everything is API driven.  Automate and test your highly available implementation.

####<a id="chaosmonkey">Chaos Monkey</a>

What is Chaos Monkey?  Netflix created Chaos Monkey to terminates random instances on a live server to see if an application is fault tolerant.  Not recommended to do on a live production server.

####<a id="s3">AWS S3</a>

S3 is perfect for hosting your __static__ digital assets (e.g. css, company logo, javascript files).  S3 is very efficient.

You can also use Amazon S3's website feature when only client-side processing is required.  There's no infrastructure to configure/launch.

####<a id="cloudfront">AWS CloudFront</a>

__CloudFront__ is a world-wide __content distribution network__ (CDN).  This distributes content to end users closest to the user so its low latency, high data transfer speeds (and makes use of edge locations).  Availability SLA is 

Without CloudFront, your content is being loaded from EC2 webservers directly.  With no CDN, the response time is longer and server load is higher.

You can use CloudFront for even Dynamic Resources (not just static).  This is perfect for intelligently pushing back small packets of data back to the origin server as long as it does NOT require a real time response (e.g. a voting application).  You can:
*  TCP/IP optimizations for the network path
*  Keep-Alive Connections to reduce RTT
*  Collapsed forwarding
*  SSL Termination close to viewers
*  POST/PUT upload optimizations

####<a id="route53">AWS Route 53</a>

AWS __Route 53__ is a highly available and scalable DNS (Fun note: 53 is the common port for DNS).  Route 53 manages DNS Failover to route around region and AZ level issues.  Route 53 also has domain name registration and renewals.  Route 53 should point to a __Load Balancer__.

__DNS Failover__ means that if you have a DNS pointing to a primary server and the health check fails, you can automatically reroute to a different server (even if its just a static site that says there are issues).  For example, that setup would look like this:

Record Sets
*  CNAME www is pointing to the elastic_load_balancer
*  Routing Policy = Failover
*  Record Type = Primary

Amazon S3 website
*  Routing Policy = Failover
*  Record Type = Secondary

##<a id="lab1">Lab 1 Exercise</a>

We will transform a fragile two-tier web application into a resilient, scalable application using __AWS CloudFront__.  Currently it is a web server that relies on a database server.  If either instance fails, the site is down.  If there is a surge in traffic, performance would degrade.  If we scale up, we would need to stop the instances (affecting availability).  It would also be difficult to scale horizontally by adding more web servers.

##<a id="lab1a">How to manually fix a bad instance</a>

In __EC2__, you can see that there is an Elastic IP address (__Elastic IPs__) attached to the web server (e.g. 54.175.6.128).  This enables the instance (e.g. `i-038815d0 (cloudwiki-lab1-www)`) to be reached via a static address.  A `DNS A` record is typically mapped to the Elastic IP so that you can access the server using a friendly host name like www.jobwaffle.com.

In our __EC2__, we can create __Images__ under __Instances__ > Actions > Image > Create Image.  This is done so we can launch a replacement instance in the same exact state as the current running instance (in case something goes wrong).  You can see these images under the __Images__ > __AMI__.

We will simulate a faulty instance by deleting the web server under instances.  We now replace the web server by going to AMIs, selecing the AMI we just created, and clicking 'Launch'.  We then specify the size and security groups (select the www-server security group), and the keypair.  

##<a id="lab1b">How to automatically fix bad instances</a>

AWS lets you failover to a backup website if your primary website is unavailable.  We will do this by configuring a simple backup website on Amazon S3 and use Amazon Route 53 DNS Failover to automatically route traffic when the primary site is unavailable.

_Setup Hosted Zone, Health Check, and set Failover Routing Policy_

In our __Route 53__, we will look at __Hosted Zones__, which is a hosted zone for your domain name (usually modifying these settings takes hours to take into effect).  Under 'Hosted Zones' > 'Go to Record Sets'.  You will see 3 records with these types:

*  A - this A record points to the Elastic IP Address of your web server.
*  NS
*  SOA

We want to create a DNS Failover, which first involves setting up __Health Checks__.  We add a Health Check name, IP Address (from our 'Hosted Zones' > Record Set - A record), host name, port, path, request interval, and failure threshold (10 seconds).  When you go to the Record Set, you'll see a few settings.

*  __TTL (Seconds)__ is by default set to 60 seconds.  This limits the amount of time this DNS record will be cached within the Internet's DNS system, which means there will be a shorter delay between the time failover occurs and the time that end users begin to be routed to your backup site.
*  Change the __Routing Policy__ to __Failover__.
*  For __Failover Record Type__, click 'Primary'.
*  You can __Associate with Health Check__ by clicking 'Yes' and selecting the Health Check we created earlier.

What this does is that we now automatically check the health of the homepage and verifies that it returns a successful response every 10 seconds.  If this check fails, we want to route our traffic to a backup site on AWS S3.

_Setup S3 bucket to contain a static version of the site_

Go to __S3__ and let's assume there's a bucket that has a static version of your website.  There's an option under 'Properties' that allows for __Static Website Hosting__.  Get the link for this endpoint (e.g. i-038815d0.highlyavailable.org.s3-website-us-east-1.amazonaws.com).

Go to __Route 53__ and under __Hosted Zones__, we want to 'Create Record Set'.  Under 'Alias' we select our S3 endpoint that we created right above.  We specify that the __Routing Policy__ is set to __Failover__ and the Policy is 'Secondary'.

You can now terminate the webserver and you'll see that there is a static failover (not all the links will work).  What happens is that __Route 53__ has detected the failure of the primary and is now sending traffic to the backup site.  Remember that the failover does not have to be a static site; the failover can also be directed to another active site.

##<a id="hacost">High Availability Cost</a>

Hosting a high availability website can be complex.  You want to balance between not paying more than you need to with users having a good responsive experience.  We will go over the following Web Tier Core Concepts:

*  Use Fault Tolerant AWS Services:  Amazon CloudFront, Amazon Route 53, Elastic Load Balancing (ELB)
*  Allow auto scaling for elastic capacity (e.g. be able to handle fast growth, on/off, variable peak, predictable peaks) using 'Just In Time' provisioning
*  The Web Tier Foundation Components are: Elastic Load Balancer, Amazon CloudWatch, and Auto Scaling
    -  Elastic Load Balancer has traffic come in and determines where to route traffic
    -  CloudWatch provides monitoring for AWS cloud resources; CloudWatch is a metrics repository that gets metrics like CPU utilization, latency, etc. from Elastic Load Balancer.  You can setup metric thresholds that has an action (e.g. auto-scale a new server, alert you of high latency).
    -  Auto Scaling changes configuration settings (e.g. create a new web server)

####<a id="cloudwatch">AWS CloudWatch</a>

Cloudwatch allows you to monitor AWS cloud resources.  Alarms can be setup.  You can setup metric thresholds that has an action (e.g. auto-scale a new server, alert you of high latency).  It can tell you CPU utilization, but not things like memory use (because it is an instance that thinks all memory is used).

####<a id="elb">AWS Elastic Load Balancer</a>

Elastic Load Balancer (ELB) is a load balancer that is highly available and helps with auto-scaling, does health checks, etc.  ELB spans across multiple AZs.  ELB scales smoothly based on traffic.  Scaling can take from 1-7 minutes based on traffic profile.

IP Address will change over time (so don't use IP Address).
*  Use 'CNAME' records in DNS
*  DNS TTL of 60 seconds

For spiky/flash traffic, pre-warm ELB by submitting a request to AWS Support.  You can also DIY by slowling simulating more users.

There are two types of ELBs: __public ELB__ (only ELB is public facing, Web/app instances can use private IP Adddresses in private subnets) and __internal ELBs__ (ideal for balancing request between multiple internal tiers).  

####<a id="autoscale">AWS Auto Scaling</a>

With auto scaling, we want to scale up and down or rebalance servers across AZs.  We have the following types of scaling (manual, by schedule, by policy in response to real-time alerts, auto-rebalance across AZs).

For example, scale up by 10% if CPU utilization is greater than 60% for 5 minutes or scale down by 10% if CPU utilization is less than 30% for 20 minutes.  We always scale up more since it takes longer than scaling down.

####<a id="autoscalecomponents">Auto Scaling Components</a>

We have the following components:
*  Launch Configuration - describes what auto scaling will create when adding instances
*  Auto-Scaling Group - auto scaling managed grouping of EC2 instances
*  Auto-Scaling Policy - parameters for performing an auto scaling action

We can use the __AWS CLI__ for auto-scaling by calling commands like an API (or you can see this under __EC2__ auto-scaling:

E.g. `aws autoscaling create-launch-configuration --launch-configuration-name LC1 \ --image-id ami-570f603ee --instance-type m3.medium`

`aws autoscaling create-auto-scaling-group --auto-scaling-group-name ASG1 \ --launch-configuration-name LC1 --min-size 2 --max-size 8 \ --desired-capacity 4 --availability-zones us-east-1a us-east-1c --load-balancer-names ELB1`

####<a id="bootstrap">Bootstrapping</a>

Configuration (like with Chef) of setting up a server when it first starts.

##<a id="lab2">Lab 2 Exercise - Create High Availability on Web Tier</a>

####<a id="lab2a">Load Balancers</a>

We will continue our previous web application and remove the single point of failure at the web app by adding a __load balancer__ and implementing __auto scaling__.  We will start with the 'Deployment & Management' > CloudFormation > Output; notice that this URL resolves to a Elastic Load Balancer.  Under __EC2__, look at the __Load Balancers__ section.  Under the 'Instances' tab, you can see that there are two Availability Zones (AZs); e.g. we have us-east-1c and us-east-1d.

####<a id="lab2b">Auto Scaling Part 1</a>

So what happens if an instance in an AZ fails?  The other instances have to carry the load.  We can implement __auto scaling__ to help correct for this.  We simulate an instance stopping by stopping a web server instance.  The instance will fail an ELB health check and ELB will remove this instance from rotation.  

After a few minutes, Auto Scaling will find the unavailable instance, terminate it, and launch a replacement instance, then register it with ELB.  You can see this in action in __EC2__ under __Auto Scaling Groups__ and look at the 'Activity History' to see that an instance was terminated, then a new EC2 instance is automatically launching.  

####<a id="lab2c">Auto Scaling Part 2</a>

We can generate load on our web servers to trigger Auto Scaling grow the number of servers to handle the load.  To see how a group scales, in __EC2__ look under 'Details' to see the 'desired', 'min', and 'max' servers.  To see the policies, look under 'Scaling Policies' to see the rules (e.g. remove 1 instance when CPU utilization < 40 for 180 consecutive periods of 60 seconds).

We run a custom program (bees with machine guns) that spins up EC2 servers that generate load on our web servers.  You can see the load in real time in __EC2__ > Instances > select a web server > Monitoring.  To see a list of events, look under 'Auto Scaling Groups' > 'Activity History' to see the web servers increase and decrease in usage.

####<a id="lab2d">Summary: High Availability on Web Tier</a>

Summary: The current architecture improves availability and solves the single point of failure at the web tier (via Elastic Load Balancer), using auto scaling to provide fault tolerance and scalability within the web server fleet.
*  However, the database is still not fault tolerant.  A database failure will cause an application outage.
*  Session state is held in each of the web tiers so while the application can survive the loss of web servers, the state on the lost web servers won't be recoverable, which could result in a poor customer experience.

##<a id="storage">AWS Storage Options</a>

AWS has a few storage options including scalable storage, inexpensive archive storage, persistent direct attached storage, turn-key gateway solutions.  You want to pick the right one for the job.  Each has a unique combination of performance, durability, cost and interface.

*  AWS EC2
    -  EC2 Local Instance Storage (Ephemeral volumes)
        +  Magnetic
        +  SSD
    -  EBS (Elastic BLock Storage)
        +  Magnet, General Purpose (SSD) or PIOPS (SSD)
*  Cloud Storage
    -  S3 (Simple Storage Service)
    -  Glacier (Archival/Cold Storage)

####<a id="ebs">AWS Elastic Block Store (EBS)</a>

High performance block storage device, can mount as drives to instances.  However, can't map to multiple instances.  This is essentially a network attached hard drive.  These are 1 GB to 16 TB in size, are private to your instances, and are replicated within an Availability Zone.  Backups can be snapshotted for point-in-time restore.  Detailed metrics can be captured with CloudWatch.

__EBS Availability__

A volume is replicated, but only within a __single Availability Zone__.  Snapshots are stored in S3.  You can increase availability by replicating your volumes to another AZ or Snapshot regularly.

__EBS Performance__

There are two types of EBS, Magnetic and SSD.  Recommend not using Magnetic.
*  3 IOPS/GB sustained/baseline
    -  100GB = 300 IOPS, 1000GB = 3000 IOPS sustained
*  Each volume gets an IO credit of 5.4 million IOPS
*  Burstable up to 3000 IOPS for 30 minutes
    -  30 * 60 * 3000 = 5,400,000 IOPS
    -  Once IOPS are depleted, it's replenished at the baseline rate
*  Sustained performance until IOPS are depleted

Use PIOPS for consistent IO performance
*  Up to 4000 16KB IOPS/volume
*  Stripe multiple volumes for >4000 IOPS
*  Use with EBS Optimized Instances (be aware of max IOPS and bandwidth based on your EC2 instance size, e.g. c1.xlarge = 1,000 dedicated EBS throughput)

####<a id="instancestorage">AWS EC2 Instance Storage</a>

'Instance Storage' is a storage local to your AWS EC2 instance.  These are basically hard drives that you can't take with you (i.e. you lose this when your server shuts down) with the following properties:
*  Temporary, volatile block storage
*  Not all instances have instance storage (e.g. T2)
*  Must be configured at launch of instance, cannot be reconfigured later
*  Good for sequential I/O whereas EBS is better for random I/O
*  Ranges from 4GB to 48 TB of storage (based on instance type)
*  Can be used for transient or replicated data
*  Good for swap, caches, temp tables, intermediate data, data you can afford to lose
*  SSD Instance storage ideal for high performance workloads
    -  Up to 365,000 4KB random read IOPS and up to 315,000 4KB random first write IOPS
*  Neither highly available nor Fault Tolerant
*  There is a first access penalty
    -  First time you access a block of storage, it must be either wiped clean (for new volumes)
    -  Or instantiated from its snapshot, before you can access the block (for restored volumes)
*  Recommended use is for high performance workloads

####<a id="s3storage">AWS S3 Storage</a>

Very very high durability of objects.  Unlimited storage of objects of any type.

*  Buckets act like hard drives
*  Up to 5TB per object
*  Can have object versioning
*  Can do server side encryption
*  Can do browser upload to S3
*  S3 is a key-value store; key is the file name and value is the object.
    -  E.g. `<my_bucket>/521335461-2013_11_13.jpg`
    -  E.g. `<my_bucket>/465330151-2013_11_13.jpg`
    -  You want the first few numbers to be random, that way it'll hit different shards.  The WORST thing you can do is make these numbers increment by one.  Note: Only for really high volumes like 1TB+.
*  Add additional prefixes to help sorting, that way one application will not degrade another application's performance
    -  E.g. `<my_bucket>/images/5213213-2013_11_13.jpg`
    -  E.g. `<my_bucket>/images/9686894-2013_11_13.jpg`
    -  E.g. `<my_bucket>/movies/4324678-2013_11_13.jpg`
    -  E.g. `<my_bucket>/movies/9066548-2013_11_13.jpg`
*  Lookup image for AWS Storage Options Chart

####<a id="storagesummary">AWS Storage Options Summary</a>

*  AWS storage options are different from traditional storage options
*  EBS is fault tolerant to a single AZ, important to snapshot regularly and/or replicate critical data
*  Use EBS with Provisional IOPS for higher performance file systems
*  Use Amazon S3 for Fault Tolerant object storage with high durability
    -  Object Storage, no infrastructure to think about
    -  Unlimited space, high scale

##<a id="dbarchitecture">Database Options</a>

AWS supports a variety of database deployment options.  
*  AWS can support traditional database high availability options by mirroring and replication.
*  AWS also offers managed databases with high availability support using __Amazon RDS Multi-AZ__ and __Amazon DynamoDB__.

####<a id="diyrdbms">DIY RDBMS options</a>

Each option solves different DB problems so choose based on experience, features, and cost.

Self managed on EC2
*  Oracle, SQL Server, DB2
*  MySQL, PostgreSQL
*  Basically any type of database

AWS Managed (RDS)
*  RDS MySQL
*  RDS Oracle
*  RDS SQL Server
*  RDS PostgreSQL

####<a id="dbstorageconsiderations">Database Storage Considerations</a>
*  Use EBS GP2 volumes for low/medium workloads
*  EBS PIOPS provides up to 4000 IOPS/volume for high performance workloads
*  Instance storage for temporary data
*  SSD Ephemeral disk amy be useful for NoSQL databases or replicated RDBMS
*  For DR purposes, important to snapshot EBS volumes regularly
*  For best practices, read whitepapers.

####<a id="caching">Caching</a>

You can always cache to reduce the number of reads to your database.

####<a id="dbgenericreplication">Database Relational Database Replication</a>

You can have a typical master-slave setup.  You might have database mirroring.  This allows for reporting to hit a slave database instead of the master.

####<a id="sharding">Database Sharding</a>

You can shard your databases where you split large partitionable tables across multiple, smaller database servers.  You need to setup application so it is shard-ware and shards may require periodic rebalancing.  This also brings additional challenges like multi-server querying.

####<a id="dbnosql">NoSQL Databases</a>

If you don't need these important features (e.g. transaction support, ACID compliance, joins, SQL) you can then switch to key-value store using NoSQL (very fast, no need to worry about same sharding issues as a relational database).

####<a id="rds">AWS RDS</a>

AWS has a relational database called __RDS__ that has an option to one-click high availability.  This creates a replicated database to another availability zone.

####<a id="dynamodb">AWS DynamoDB</a>

AWS __DynamoDB__ is a fully managed NoSQL database service that provides extremely fast and predictable performance with seamless scalability.  There is minimal administration, low latency SSDs, and unlimited potential storage and throughput.  There is no need for tuning, is highly durabile, and one of the few services that is fault tolerant (only other is Route 53).

*  Provisioned the throughput needed for each table; set at table creation (e.g. my table needs 1,000 writes/second and 5,000 reads/seconds of capacity)
*  Increase / decrease any time via API call
*  Pay for throughput and storage
*  Good uses cases include low latency and/or high throughput are required
*  Digital advertising: cookie/pixel tracking
*  Any workload where high availability and performance are important
*  Most operations should be lookup in a table (key-value), not for relational data
*  No dependencies (e.g. foreign keys)

####<a id="dbsummary">AWS Database Summary</a>

*  AWS supports a wide variety of database and high availability options
*  Consider whether you want to manage your database infrastructure or let AWS do it
*  RDS provides a high availability relational database
*  DynamoDB provides a high availability and fault tolerant NoSQL database 

##<a id="lab3">Lab 3 Exercise - Create High Availability on Database Tier</a>

####<a id="lab3a">AWS RDS to create highly available relational database</a>

Our goal is to create a highly available database tier.  We will use AWS __RDS__, where Amazon will run the database instance in multi-AZs.  When looking at the database, if __Multi-AZ__ option is 'Yes', you will see the 'Availability Zone' and the 'Secondary Availability Zone'.

Failover will automatically occur when various events (like rebooting the server) happen.  Normally a reboot would cause downtime, but with a Multi-AZ instance, RDS can failover to the standby instance while the primary is rebooting (When you reboot, there will be an option to select 'Reboot with Failover', which you should check).  You can verify what happens by checking the 'Events' log.  There are also options to create __Read Replicas__ and __Automatic Database Backups__.  It is as simple as clicking on 'Instance Actions' > 'Create Read Replica'

####<a id="lab3b">AWS DynamoDB (NoSQL) to store session state</a>

We lose session state in case of web server failure.  This is also an issue when using Auto Scaling because auto scaled instances should be stateless.  There are many possible solutions to store our session state information including putting this outside of web servers in a database, in an in-memory cache (like memcached or AWS ElasticCache), or a high performance durable storage.

For this example, we will store our sessions state using AWS __DynamoDB__ since it is inherently fault tolerant, we do not need to worry about replication, failover or any other issues with high availability.  We enter in some data into our application (e.g. create a login, password, etc).  We go back to 'DynamoDB' and look at the table to see the id and data.  By default, there is a provisioned throughput of 10 read and writes per second, which you can change depending on your needs.

####<a id="lab3c">Lab Summary: Use AWS RDS or DynamoDB</a>

In summary, it is easy to create high availability of databases if you use AWS RDS or DynamoDB.  You have the option to create your own custom databases and manually setting master-slaves, but that is a lot more difficult.

##<a id="hadesignpatterns">High Availability Design Patterns</a>

When it comes to High Availability, we have the following patterns:
*  Basic patterns
*  Advanced patterns
*  Amazon Virtual Private Cloud (VPC) patterns

####<a id="commondesignpatterns">Common Design Patterns</a>

__Multi-Server Pattern__
*  Problem: Need to increase availability at the instance layer such that if an instance dies there is a resilient solution.
*  Solution: Distribute load between instances using Elastic Load Balancing

__Multi-Datacenter Pattern__
*  Problem:  Increase availability of my application if there is a power outage or a natural disaster in the area causing a data center outage
*  Solution: Distribute load between instances using an Elastic Load Balancing across multiple AZs.

__High Availability Database Pattern__
*  Problem:  Need to have high availability solution that will withstand an outage of the DB master and can sustain high volume of reads
*  Solution:  Deploy RDS with a master and slave configuration.  In addition, deploy a read replica in each availability zone for my reads.

__Floating IP Pattern__
*  Problem:  If my instance fails or I need to upgrade it, I need to push traffic to another instance with the same public IP Address.
*  Solution:  Use an IP address which allows portability of a given IP Address between EC2 instances without updating DNS

__Floating Interface Pattern__
*  Problem:  If my instance fails or I need to upgrade it, I need to push traffic to another instance with the same public and private IP addresses and same network interface
*  Solution:  Deploy your application in VPC and use an elastic network interface (ENI) on eth1 that can be moved between instances and retain same MAC, public, and private IP addresses.

__State Sharing__

*  Problem: State is stored on my server so scaling horizontally does not work that well.  I want my application to be stateless.
*  Solution:  In order to scale horizontally and not have a user locked into a single server, we need to move state off of my server into a KVS.

__Web Storage Pattern__

*  Problem:  Delivery of large files from a web server can become a problem in terms of network load
*  Solution:  User generated content needs to be distributed across all my web servers.

__Scheduled Scale Out__

*  Problem:  Traffic does not scale organically, but has huge jumps at specific periods of the day or for an event (e.g. Shazam commercial to use app for Super Bowl)
*  Solution:  Use Scaling by Schedule or Scaling by Policy to handle running an Auto Scaling policy at a specific point in time.

__Job Observer Pattern__

*  Problem:  I want to manage resources against the depth of my work queue
*  Solution:  Create Auto Scaling group to scale compute resources based upon queue depth

__Bootstrap Instance__

*  Problem:  Code releases happen often and creating a new AMI every time you have a release and managing these AMIs across multiple regions is difficult
*  Solution:  Develop a base AMI, and then bootstrap the instance during the boot process to install software, get updates, and install source code so that your AMI rarely or never changes.

__High Availability (HA) NAT__

*  Problem:  NAT inside of VPC is confined to a single instance, which could fail
*  Solution:
    -  Run NAT in independent ASGs per AZ
    -  If NAT instance goes down, Auto Scaling will launch new NAT instance
    -  As part of launch config, assign a public IP and call VPC APIs to update routes

__HA NAT - Squid Proxy__

*  Problem:  Standard NAT inside of VPC is confined to a single instance, which could fail
*  Solution:
    -  Run Squid in proxy configuration in an ASG
    -  On boot, configure instance to point to proxy for all HTTP(S) requests

####<a id="vpndirectconnect">VPN and AWS Direct Connect</a>

__VPN Connectivity__ allows connecting dual redundant tunnels between your on-premises equipment and AWS

__AWS Direct Connect__ establishes a private network connection between your network and one of the AWS Regions.  AWS Direct Connect is an alternative to using the Internet to access AWS cloud services.  This reduces bandwith costs, creates consistent network performance, is a private connection to your Amazon VPC.

##<a id="lab4">Making outbound traffic highly available using NAT instances</a>

In previous labs we created redundant services across availability zones (AZs) within a region and distributing inbound traffic across those services at various application tiers (web, database).  Now we want to look at how to make outbound traffic highly originating from application tiers in VPC highly available, using NAT instances that span multiple AZs.

This looks tough, time to call an Amazon representative if you run into this issue.


