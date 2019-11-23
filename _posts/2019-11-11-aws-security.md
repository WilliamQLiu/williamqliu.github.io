---
layout: post
title: AWS Security Training
---


# {{ page.title }}

## AWS Overview

AWS is broken down into (largest to smallest pieces):

* Partitions (e.g. AWS Standard with `aws`, AWS China with `aws-cn`, GovCloud `aws-us-gov`, AWS Secret)
* Regions (e.g. us-east-1 = Northern Virginia, us-east-2 = Ohio)
* Availability Zones (AZ) with 2-6 AZs make up a region (e.g. us-east-1a), are fault tolerant
* Data centers - AZs are made up for data centers

Most services are regional. You mainly deal with AZs in regard to VPC related service decisions.

### Different Regions

`us-east-1`

* `us-east-1` (aka `us-tirefire-1`), believed to have the most problems because more people notice them
  because more users are there
* the region with the most AZs
* 'global' services are tied to us-east-1 so if there's a problem, assumed problems everywhere

`us-west-1`

* ~15% more expensive
* low latency to Bay area

## AWS Services

API calls are getting increasingly complex.

  Service   # of functions
  EC2       352
  IOT       167
  ...

### Global Services

Most services are regional, but some are global, like:

* IAM
* Route53

## Commonly confused terms

An aws __account__ contains IAM __users__

## AWS "Shared" Responsibility Model

Summary: Anything you can secure, is your responsibility to secure.

* AWS cares that things are secure by default
* AWS is not concerned about footguns... until they make the news about open S3 buckets

Customer - responsibility for security 'in' the cloud
  Customer Data
  Platform, Applications, Identity and Access Management
  Operating System, Network
  etc.

AWS's problem:

* prevent guest-to-host escapes of hypervisor
* ensuring services can't be hacked

Your problem:

* ensuring your EC2 OS and installed applications are up-to-date, configured securely, monitored
* ensuring your data is encrypted
* ensuring your S3 policies are correct
* AMI (Amazon Machine Image) does not have a virus

Just because something is hosted on AWS, does not mean it is AWS' problem

Basically, the AWS security responsibility is not perfect security.

### Monoculture

AWS provides the same security all accounts (unless you pay to have your own isolated partitions like the
US gov, ~$600M). No one gets to visit their data centers. Everyone on AWS is running in the same 'version' of AWS
(same APIs, same software)

### AWS does some additional security benefits

* Gets advanced notifications of vulnerabilities (Xen pre-disclosure list)
* Uses Automated Reasoning (Firmware verification, Crypto verification)
* Buys their datacenters under shell companies
* Has a dedicated DDoS response team

### AWS Rules

* 'AWS will not break businesses' (e.g. if your spending suddenly spikes, they will not take action, just alert you)
* 'No AWS services will stop working'

### Resources for keeping up with AWS news

Last Week in AWS - https://lastweekinaws.com
CloudSecList - https://cloudseclist.com
og-aws: https://og-aws-slack.lexikon.io
@awswhatsnew (posts announcements)
@AWSSecurityInfo
@jeffbarr (AWS Evangelist)
'Securing DevOps' book by Julien Vehent

### How AWS contacts you

AWS communicates only to the root account email address

* You might want to put this root account email address as a distribution to other emails
* Your TAM (technical account manager, requires $15k+ a month) may also be aware of issues
* Communications you might care about include (AWS keys on github)
* Occasional mass emails like Public S3 buckets, Public EBS snapshots and AMIs

## Account Recovery

* Access to root email + phone allows you to remove the root MFA and change the password to login. Takes a minute.
* If no MFA, only email access is neeeded
* For the root account email, you should use a shared email distro

## MFA (Multi-Factor Authentication) Options

    Supported MFA solution  Pros                                    Cons
    Hardware Token          Forces physical access                  Phishable, timing gets out of sync, lost
    QR code                 Free                                    Phishable, can be copied to anywhere, 2FA -> 1FA
    U2F                     Forces physical access, not phisable    May be lost

Remember that the QR code is just a representation of data (it is just text)

## Backups

Do not put your backups in the same place as the data being backed up

Case Study:

* Similar to Github
* Attacker compromised their AWS account, everyone locked out, deleted all backups
* Company shutdown within 12 hours
* Put backups in minimally a separate account and optionally a separate cloud provider
  e.g. prod is in us-east-1 and backup in us-west-2

### Disaster Recovery

S3 durability is 99.999999999%

* Still, 1 object is lost per year for every 100B
* Stored across multiple AZs
* Stays with the region

S3 allows cross-region replication and life cycle policies

### Not all resources can be backed up

* 'Elastic' IPs and domain names are hard to get back
* 'Elastic' IPs are static IPs that will not change

### AWS Backup

__AWS Backup__ is a service to automate common backup tasks (e.g. RDS, DynamoDB)

Consider how long it takes to restore a backup.

### Thundering Herd

If everyone evacuates one region, other regions will experience trouble (e.g. everyone will start moving
off the failure region and try another region, which will cause say EC2 times a while to start)

### How can I tell if something is down?

AWS status page shows service status, but usually everything is always green (e.g. if S3 down, this page will not update)
Instead, use the Personal Health Dashboard
A lot of monitoring companies will be quiet when AWS is down.

## SLAs

S3 SLA is for access not durability

### SLA Math

You need to multiple when at least one of your apps is down

ELB = 99.99% * EC2 = 99.99% * EBS 99.99% * RDS 99.95% = 99.92% Total Time

### AWS service baseline

Remember that AWS works in the max 2 pizza size team rule. There really isn't a PM that works across teams.
A feature might be released and not have 'simple' cross functional services (e.g. no access logs)

Basically, you want to wait a while before using a new service that AWS releases.

## VPC Network Communication

* Packet sniffing and ARP spoofing does not work in VPCs
* Traffic is authenticated by the hypervisor system of VPCs so an attacker cannot spoof messages
* AWS only started encrypting traffic between data centers in May 2019
* Note: A subnet can span multiple data centers
* Traffic within services may not be encrypted

## Encryption

Many services have an ability to encrypt data at rest, but others you have to opt in.
You cannot really tell after you hit 'encrypt' option because AWS does not tell you much about what it is
doing for encryption. There is no way to prove the data really is encrypted, how keys are managed, etc.

### Key takeaways

* Beware of phishing and account take-over
* Have backups and a disaster recovery plan
* Beware of thundering herd, when a region goes down, hard to spin up in new region because everyone else is too

## Public S3 buckets

You can see in S3 'Access', there is 'Public'.
You can have bucket permissions through:

* __ACL__ - old access control
  'AllUsers' means public
  'AuthenticatedUsers' is ALWAYS bad (means if you are logged into ANY AWS account)
  Do not use ACLs anymore
  Objects (files) do not inherit from the ACLs
  Just use bucket permission policies
  No way to know what permissions are with ACLs without iterating through all objects
* __Bucket Permission Policies__ - allows finer grained access
  Preferred way to implement access now

Services that work with Resource Based policies (e.g. EC2s)

### Resources

* S3 is a global namespace so that is why it is so dangerous (e.g. no one else can have a bucket named 'flaws.cloud'
  if you have it already.
* Other resources require at least knowing the account ID
* AWS made poor choices with ElasticSearch - can be scanned for with __Shodan__

### Why are so many S3 buckets been made public?

* Easy to share public bucket
* Cyberduck used to make any S3 bucket it created public by default
* People mistook what AuthenticatedUsers meant

### S3 Public Block

Denies S3 buckets account-wide or select buckets from being made public
Beware that AWS's definition of Public is not yours
  If the bucket allows access from a single IP, it is not public.
  Also not public if it is a `/1`
If the bucket allows access from another account, it is not public.

### Key takeaways

* Many services on AWS can be made public other than S3 buckets (e.g. RDS, EC2 images)
* Beware that AWS definition of public is not the same as your definition

## Logs
                What        Delay           Storage
CloudTrail      API calls   15 minutes      S3
VPC Flow Logs   etc

### VPC Flow Logs

VPC Flow Logs are awkward and much less helpful than you assume

* Log status is often SKIPDATA, meaning AWS had an internal error
* Sometimes shows traffic is blocked when it isn't
* IP shown is always the internal IP
* Use for debugging; try to understand if security groups or NACLs are blocking traffic
* Can detect systems talking to unexpected IPS
* tcp flags can be used to determine source and destination

### VPC Traffic Mirroring

Full packet capture
If traffic is encrypted, will not magically decrypt traffic
Specifies a listener and sends packets to UDP port 4879

### CloudTrail

What is it?

* Records AWS API Calls (e.g. create EC2 instances)
* Not enabled by default

Cloud Trail tells us:

* Who - (userIdentity ARN, sourceIPAddress, userAgent)
* What - (eventSource, eventName, requestParameters)
* When - (eventTime)

Log Options include:

* CloudTrail - Nearly free, recommend to turn on
* Organization Trails (not enabled by default), but good idea to
* CloudWatch Events - does real time, but not read calls (a subset of CloudTrail logs) - nearly free, recommend to turn on
* CloudTrail Event History - usually not used; shows create, modify, and delete activities

There are things CloudTrail records where there is no API calls (e.g. console logins)
CloudTrail does not record things like sshing into a server
There is not a strict 1:1 ratio of IAM privileges to API calls (e.g. dax.CreateCluster requires 11 IAM privileges)
IAM vs API vs CloudTrail frequently has names that do not match with the service names CloudTrail records
  e.g. CLI command: aws s3 ls    =   IAM Privilege: ListAllMyBuckets    =   API Action: ListBuckets

### IAM

Use the aws site for generating IAM policies

https://awspolicygen.s3.amazonaws.com/policygen.html

More data is available by parsing the docs

### API Data

Look at botocore

#### Best Practices for CloudTrail

Your Prod AWS account is separate from your CloudTrail AWS account (that way if something is compromised on your
prod account, a CloudTrail AWS account can still see access)

### GuardDuty

Best monitoring system for detecting compromised systems in AWS. Data sources include:

* CloudTrail
* DNS
* VPC Flow Logs

Uses 'machine learning', but probably "This hasn't been seen for 30 days"
Uses 'Threat Intel', which is mainly "Here's a list of bad IP Addresses that we see being used"

GuardDuty is regional (need to turn on per region)
Usually less than 1% of your total AWS bill
GuardDuty has a master/member concept for multiple accounts, but not necessary and cannot aggregate between regions.

__Master/Member__ Accounts - look up, but not recommended to use; see CloudWatch Events Aggregation

Basically, you should use GuardDuty. It detects a lot of ways that a system might be compromised and this
extends to say 'Access Denied' from services, changes not done through Terraform, etc. Can also alert on high
impact areas (e.g. DeleteVPC, DeleteDBCluster) or known rare calls (e.g.iam:Create, accessing backups, modifydbinstance password)

### CloudWatch Events aggregation

Turn on cloud watch event rules, that then goes to your 'security' aws account which sends to kinesis stream and
create a rule to send the events to. Have a lambda send the events in the kinesis stream to another account (say
us-east-1)

Then on us-east-1, have the events sent to StreamAlert which analyzes and does some rules (e.g. alert)

## Alerts

You can set up your own detections to see if there are any bad security practices:

* A security group with 0.0.0.0/0
* A S3 bucket with ACL open to AllUsers
* Changes not done with terraform based on user-agent
* If we require AWS CLI actions happen through a VPN
* Flatline alert if no logs come in

### Security Alerts

* Test regularly
* Put the in source control with unit tests
* Block or auto-remediate when you can

Alert Levels

* Information - log a message to a chat room, no response needed
* Warning - Create a ticket, take action within 24 hours
* Critical - Page you, take action within 15 minutes

Ticket your alerts, will help you learn:

* What rules fire most often?
* Which cause the most False Positives?
* How long do they take to respond to?
* What time of day does this happen?
* Does everyone respond to tickets the same?

### Actionable Alerts

* Make your alerts actionable (e.g. specify what host)
* Make your alerting code do lookups for you (e.g. security group sg-12345 changed)
* Aggregate similar alerts

Good developers document their code.
Good defenders document their alert rules.

### Querying Logs

You can:

* Download, gunzip, then either look with `jq` or `pre-process, ingest, and use elasticsearch or splunk to search
* Download to a S3 bucket and use Athena
    Athena gotchas include: Takes at least a few seconds per command and queries can take variable amounts of time
    If you are running the same queries repeatedly (like every minute), then might be better idea to download locally and run
    query with say elasticsearch

### JQ

Few ways to use JQ:

    $cat *.json | jq '.Records[]|eventName'

    # show in tab
    jq -r '.Records[]'

    # Can import jq with Python
    import pyjq

### How to respond to Alerts

* Review your logs so you can undo bad actions (just keep in mind that CloudTrail does not record all actions)
* Roll your keys AND deny old sessions
* If the hacker did not s3:ListBuckets, they probably did not access them
* For EC2 instances, take a disk snapshot that you can analyze in an isolated VPC
* Close off network connections
    Security Groups are stateful - if an attacker has a connection, it will not be closed
    NACLs are stateless, but hard to apply to a single EC2
    DNS exfil is possible unless you disable it for the VPC
* Remove the IAM role for the EC2 (but keep in mind your EC2 is probably doing something)
* Check if there are any other credentials on the system (API Keys, IAM user access keys)

Netflix has Diffy which can help check if any resources are compromised during an incident
https://github.com/Netflix-Skunkworks/diffy

### Key takeaways

* Turn on CloudTrail and GuardDuty
* VPC Flow Logs are not as useful as you think
* Have a good system for responding to alerts
* Learn how to search through logs

## IAM

* AWS credentials has a root user email and password + optional MFA
* IAM Users
  * Username and password + optional MFA
  * Access key and secret key

### Access keys

* Considered bad because access keys never expire and have to be disable or deleted from the user
* If the user has an MFA, this is only enforced on web login, not on the access keys
* Stored in user's home directory in plain-text

### Session Keys

Acquired when you assume a role or use an IAM role by a service

* Nothing restricts them to being used only from that EC2
* Have an expiration date

### Secret Key

Only the secret key needs to be kept secret.
The access key and session token will show up in CloudTrail logs, but not the secret key.

### boto

Python SDK used by the AWS CLI and many tools for interacting with AWS is with boto

### aws-vault

https://github.com/99designs/aws-vault

Tool used to securely store and access AWS credentials in a development environment.
Uses the macOS keychain or an encrypted file, keeps secrets out of ~
Has some issues with needing to repeatedly type in your password

### Configuring IAM roles

In `~/.aws/config`, you can configure role assumptions

    [default]
    output=json
    region=us-east-1

    [profile prod]
    role_arn=arn...

    [profile stag]
    role_arn=arn...

### SSO

You should use SSO, but not AWS SSO. Use Segment's version of AWS OKTA

https://github.com/segmentio/aws-okta

### Magical IP Address

The IP Address `169.254.169.254` is a magical IP in the cloud world (for AWS, Azure, DigitalOcean, Google) that
allows cloud resources to find out metadata about themselves.

* EC2's get IAM roles using this magical IP Address: `169.254.169.254`
* RFC 3927 describes Link Local communications
* This traffic cannot be blocked with Security Groups or NACLs

IP Addresses:

* `169.254.169.254` instance metadata (allows EC2s to get a session token)
* `169.254.170.2` is creds for ECS (basically container version)

### IAM differences

* Users, Roles, and the Root User are Principals
* Users can be members of Groups
* Policies can be applied to Users, Roles, or Groups

#### Users vs Roles

* Users can have passwords and access keys
* Roles can be assumed into by Users or other roles

#### Why use IAM instead of the root creds for everything?

* IAM allows you to implement a Least Privilege strategy
* IAM allows auditing

### IAM Policies

Different actions support different conditions

Consider using an IAM linting library to check if policies are valid or if there are bad policy patterns

### Attribute Based Access Control (ABAC)

Restricts people to only certain application pipelines using tags

* AWS is pushing this, but due to lack of tooling, recommend avoiding
* E.g. person tagged with the 'star' policy can have access to the 'star' resources
  person tagged with the 'moon' policy can have access to the 'moon' resources
* Limited use cases
* Caveats include needing the ability to tag on creation, not just the ability to tag (and some resources do not have that ability)
* Another cavest is you can try a naming convention, but 20 char limit

### IAM Limits

Ideally you would specify exactly

* which actions
* which resources
* which conditions

But managed policies are limited to 6144 characters

### AWS Managed Policies

* AWS provides some policies for common uses cases
* Most of these are over-privileged, but some are under-privileged
* Use as a starting point only
* Keep in mind, instead of `ReadOnlyAccess`, you probably want `ViewOnlyAccess` (to view metadata only; list bucket, get metadata)

### Best Practice

Allow user to add their own MFA device and restrict access for other actions unless MFA is active

### IAM Permissions Boundaries

An __IAM permissionsboundary__ is used to set the boundary for an IAM entity (user or role). This limits the
maximum permissions for the user or role.

### Resource Policies

__Resource Policies__ control how the specified principal can access the resource to which the policy is attached

### Organization SCPs

__Service Control Policies (SCPs)__ are the only way that even the root user can be stopped from doing something
Master account cannot be blocked via SCP on itself.

### Session Policies

When you assume a role, you can assume a session policy to reduce the scope of the privileges you would have.
E.g. I want to delete a bucket, but be careful that I do not delete these other critical buckets, I might assume
a role.

### Best Practices

* Implement Least Privilege Strategy (being able to read an S3 bucket is of no value if you do not know bucket exists)
* `aws:SourceVPC` - requires the use of VPC endpoints which only S3 and DynamoDB support
* `aws:UserAgent` - can act like a shared secret, use for detection

### VPC endpoints

* Only resources within a VPC can access them
* Gateway endpoints (only 2 services: S3 and DynamoDB)
* Interface endpoints (Private Link) - 30+ services supported

### Honey Tokens

* Create an IAM User with no privileges
* Create Access Key
* Put key somewhere interesting
* Detect when it is used

SpaceCrab - Atlassian generates an access key for honey tokens
CanaryTokens - Create fake AWS keys, email addresses, etc
Rhino Security Labs - honeytoken usage detected via Cloudtrail

### Key takeaways

* Ideally use SSO
* IAM policies can become complex
* If your IAM policies are becoming too complex, consider using different AWS accounts
* Do not give access to list all things if you do not need to (that way people will not know the item exists)
* Use conditions to check and use aws:MultiFactorAuthPresent
* Do not use * if possible

## Open Source AWS Security Tools

https://github.com/toniblyx/my-arsenal-of-aws-security-tools

### S3 bucket defense ideas

* You can append or prepend a random hash to your bucket names, but its awkward
* Best recommendation: Prevent your buckets from being made public
* Watch for Bucket sniping (e.g. Athena will always use `aws-athena-query-results-ACCOUNTID-REGION` and
  will register, then try to read the results of that bucket)

### Auditing Tools

Tool                Author
ScoutSuite          nccgroup - focused on pentests
Security Monkey     Netflix - EOL, first regular scanning tool for security teams
AWS Config Rules    AWS - easy to setup, but may be difficult to configure to your needs
Cloud Custodian     Capital One - first aws-first tool, auto-remediation, difficult to configure
Prowler             CIS (Center for Information Security Benchmark) - easy to use bash tool
CloudMapper         Duo Labs
PacBot              T-Mobile (but costs $1000 a month to deploy)
StreamAlert         Airbnb - Use terraform to set up an application based on Kinesis Streams and Lambda to receive and analyze logs
                    Uses Rules as code so it is pretty awesome

No tool is a clear winner, each tool has a dozen unique checks
Be careful with scanning accounts regularly - can cause CloudTrail logs 10x in size, AWS can rate-limit you for too many queries

#### AWS Config Rules

* AWS Config is supposed to get a snapshot of the metadata of your account
* Price based on the number of times they are evaluated
* Can make organizational rules

#### AWS Trusted Advisor

* Checks for security, cost optimization, fault tolerance, and when you're nearing service limits
* No configuration ability so you'll repeatedly have alerts for things like under-utilized EC2's
  because you are not using 100% of the CPU
* Easy to read Dashboard

### First day of using any auditing tool

* Overwhelming number of alerts on Day 1
* Add filtering to mute alerts
* Create your own rules
* Need process to contact rule breakers

### Key takeaways

* No tool is perfect, they all have different issues they look for
* Use historical access to determine need

## Pentests

Most activities no longer require informing AWS about pentesting. Just do not Dos or brute-force things.

https://aws.amazon.com/security/penetration-testing/

## Assessments

1. Ask your finance team: Who is paying?
2. Ask your TAM: What accounts use your domain for their email?
3. Ask around: Find accounts tied to personal emails using the free tier
4. Search company emails: Subject "Welcome to Amazon Web Services"
5. Search network logs: DNS to `console.aws.amazon.com`
6. Identify account relationships: Use CloudMapper 'weboftrust'
7. Perform recon (find subdomains, etc)

## Important APIs

    aws iam generate-credential-report
    aws iam get-credential-report

## Instances of hacks

Code Spaces
Instagram Million Dollar Bug

## Limiting Access

### Security Groups

* Restrict based on IP or Security Group
* Best Practice: Give all resources Security Groups (e.g. 'database', 'web app'), then restrict accesses by referencing
  the security groups, not CIDRs.

### NACLS

* Allows you to block IPs
* Should avoid using

### KMS

Key Management Service
Ensures your encryption key is never accessible
Pricing is usage based
Every key rotation increases the monthly cost

### CloudHSM

Dedicated hardware
Pricing is per hour with unlimited usage

### Inspector: Network Reachability

No agent needed, uses automated reasoning, only works on EC2

### Global Accelerator

Allows you to create direct connectivity to resources, including in a private subnet

### AWS Systems Manager

Attempt at replacing Chef, Ansible, etc. (also has a service called OpsWorks)
Have to install an agent onto EC2s

### AWS Secrets Manager

Store secrets (API keys, passwords, etc)

### AWS WAF (Web Application Firewall)

Integrates with CloudFront (caching) or ALB (modern HTTP focused version of ELB)
Pattern matching to block IP Addresses, HTTP headers, HTTP body, or UI strings and also rate-limiting

### AWS Firewall Manager

Manages WAF and Security Groups on multiple accounts

### AWS Shield

Managed DDoS protection
Every AWS account has this on
You can pay for Shield Advanced ($3k/mo + usage)

### Service Quotas

Tells you when you're getting close to limits for AWS (e.g. default EC2 limit is 20/region)
Allows you to request limit increases via API as opposed to support tickets

## Roadmap to Securing your Infra

1. Inventory
    What is in the accounts?
    Who is the point of contact? Who pays for it?
    Identify a Security Account
    Move the account into an Organization
2. Backups
    Ensure you have backups, test your backups, and that Recovery Time Objectives (RTO) and Recovery Point Objectives (RPO)
    meet your requirements
    Have backups in a separate account
    S3 Object Lock and Glacier Vaults can ensure data cannot be deleted
3. Visibility and initial remediation
    Turn on CloudTrail logs for all accounts, send to a central location
    AWS Organization Trail makes this easy
    Make sure you allow each account to see their own logs somehow
    Create an IAM role in every account to give Security view access
    Create an account initialization process
4. Detection
    Turn on GuardDuty in all active regions
    Detect issues from logs in near real-time
    Perform regular scanning of your accounts for security issues
    Document your security guidelines
5. Secure IAM access
    Use SSO for access
    Remove all IAM users
    Reduce the privileges of roles to necessary services (use Access Advisor)
    Consider using github.com/Yelp/detect-secrets to scan for env variables
6. Network attack surface reduction
    Have no publicly facing EC2s or S3 buckets
    Put EC2 behind load-balancers
    S3 buckets can be behind CloudFront
    Move all non-public network resource sinto private subnets and proxy
7. Reproducibility and supply chain management
    Control AMI and package sourcing
    Option 1: Use Salt/Puppet/Ansible/Chef to maintain configurations on your EC2s
    Option 2: Build your own AMIs, make the filesystem read-only
    Do not ssh into instances to make changes
    Host your own repo of libraries and software (do not npm/pip/yum/apt-get repos from every EC2)
    Use infrastructure as code
8. Enforce protections
    Apply SCP restrictions
      Block unwanted regions
      Protect defenses (e.g. cannot uninstall GuardDuty, the IAM role for Security)
    Automated remediation (remove unused IAM users)
    Refine IAM policies (refine the actions, resources, and conditions on IAM policies)
9. Advanced defense
    Restrict metadata access
    Setup honeytokens
10. Incident preparation
    Limit the blast radius of incidents
      Segment accounts further? Segment applications further?
    Practice responding to incidents (e.g. if an EC2 is compromised)
11. Tagging Strategy
      Important for billing, identifying owners, if something is 'Public'9. Advanced defense
    Restrict metadata access
    Setup honeytokens
12. Further restrict who can do what
      E.g. Deny ability to create IAM users and access keys
      Deny the ability for other users to setup any networking
      Have additional sign off (so if a laptop is compromised, can't merge code in)
13. Have different policies for different environments


