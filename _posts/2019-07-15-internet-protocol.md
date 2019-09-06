---
layout: post
title: Internet Protocol
---


# {{ page.title }}


## IP Addresses

Every device connected to the Internet needs to have an identifier. __Internet Protocol (IP) addresses__
are the numerical addresses used to identify a particular piece of hardware connected to the Internet.
The most common two types are Internet Protocol version 4 (__IPv4__) and Internet Protocol version 6 (__IPv6__).
__IPv6__. Some IP Addresses are reserved (e.g. for private networks).

### Network prefixes

IP Addresses (for IPv4 and IPv6) are divided into two parts:

* __Network section__ - identifies the network
* __Host section__ - identifies the node (e.g. a computer)

For example, you might have an IP address like:

    192.168.0.27

The boundary between the network address part and the host address part is determined by
the IP Address class and the NetMask or the SubnetMask.

### Allocation

IP addresses are assigned to networks in different sized __blocks__. The size of the 'block' assigned
is written after an __oblique__(__`/`__). For example, a `/16` means 64,000 IPv4 addresses and a `/26` means
64 IPv4 addresses. The lower the number aftr the oblique means the more addresses are contained in that 'block'.

### IPv4 CIDR

    Address format    Difference to last address  Mask               Addresses (Dec, 2^n)   Typical Use
    a.b.c.d/32         +0.0.0.0                   255.255.255.255    1    20                Host route
    a.b.c.d/31         +0.0.0.1                   255.255.255.254    2    21                Point to point links (RFC 3021)
    a.b.c.d/30         +0.0.0.3                   255.255.255.252    4    22                Point to point links (glue network)
    a.b.c.d/29         +0.0.0.7                   255.255.255.248    8    23                Smallest multi-host network
    a.b.c.d/28         +0.0.0.15                  255.255.255.240    16    24               Small LAN
    a.b.c.d/27         +0.0.0.31                  255.255.255.224    32    25
    a.b.c.d/26         +0.0.0.63                  255.255.255.192    64    26
    a.b.c.d/25         +0.0.0.127                 255.255.255.128    128    27              Large LAN
    a.b.c.0/24         +0.0.0.255                 255.255.255.0      256    28
    a.b.c.0/23         +0.0.1.255                 255.255.254.0      512    29
    a.b.c.0/22         +0.0.3.255                 255.255.252.0      1,024    210           Small business
    a.b.c.0/21         +0.0.7.255                 255.255.248.0      2,048    211           Small ISP/ large business
    a.b.c.0/20         +0.0.15.255                255.255.240.0      4,096    212
    a.b.c.0/19         +0.0.31.255                255.255.224.0      8,192    213           ISP/ large business
    a.b.c.0/18         +0.0.63.255                255.255.192.0      16,384    214
    a.b.c.0/17         +0.0.127.255               255.255.128.0      32,768    215
    a.b.0.0/16         +0.0.255.255               255.255.0.0        65,536    216
    a.b.0.0/15         +0.1.255.255               255.254.0.0        131,072    217
    a.b.0.0/14         +0.3.255.255               255.252.0.0        262,144    218
    a.b.0.0/13         +0.7.255.255               255.248.0.0        524,288    219
    a.b.0.0/12         +0.15.255.255              255.240.0.0        1,048,576    220
    a.b.0.0/11         +0.31.255.255              255.224.0.0        2,097,152    221
    a.b.0.0/10         +0.63.255.255              255.192.0.0        4,194,304    222
    a.b.0.0/9          +0.127.255.255             255.128.0.0        8,388,608    223
    a.0.0.0/8          +0.255.255.255             255.0.0.0          16,777,216    224      Largest IANA block allocation
    a.0.0.0/7          +1.255.255.255             254.0.0.0          33,554,432    225
    a.0.0.0/6          +3.255.255.255             252.0.0.0          67,108,864    226
    a.0.0.0/5          +7.255.255.255             248.0.0.0          134,217,728    227
    a.0.0.0/4          +15.255.255.255            240.0.0.0          268,435,456    228
    a.0.0.0/3          +31.255.255.255            224.0.0.0          536,870,912    229
    a.0.0.0/2          +63.255.255.255            192.0.0.0          1,073,741,824    230
    a.0.0.0/1          +127.255.255.255           128.0.0.0          2,147,483,648    231
    0.0.0.0/0          +255.255.255.255           0.0.0.0            4,294,967,296    232

## VPCs

You will often see IP Addresses used in a __Virtual Private Cloud__ aka __VPC__ (e.g. Amazon VPC), which allows you to
launch AWS resources into a virtual network that you have defined; think of it as a virtual network that is isolated
from other virtual networks. With VPCs, we will get into:

* Subnets
* Security Groups
* Route Tables

### Subnets

A __subnet__ is a range of IP Addresses in your VPC. For example, you can launch AWS resources into a specified subnet.
A __public subnet__ is used for resources that must be connected to the Internet.
A __private subnet__ is used for resources that won't be connected to the Internet.

For AWS, each subnet has to reside entirely within one Availability Zone and cannot span zones.


### CIDR Blocks

When you create a VPC, you have to specify a range of IPv4 addresses for the VPC in the form of
__Classless Inter-Domain Routing__ (__CIDR__) blocks. This might look like `10.0.0.0/16`. You can also assign
an IPv6 CIRD block to your VPC if you are using IPv6 addresses.

### Route Tables

Your VPC works with __Route Tables__, which are tables that enable routing within the VPC. We specify
the destination as the CIDR block and what the target is.

    VPC with 1 CIDR block
    Region = us-west-2
    VPC 10.0.0.0/16
      Subnet 1 = 10.0.0.0/17
      Subnet 2 = 10.0.128.0/17
    Main Route Table:
      Destination   Target
      10.0.0.0/16   local

    VPC with 2 CIDR blocks
    Region = us-west-2
    VPC 10.0.0.0/16 (primary CIDR)
    VPC 10.2.0.0/16 (secondary CIDR)
      Subnet 1 = 10.0.0.0/17
      Subnet 2 = 10.0.128.0/17
      Subnet 3 = 10.2.0.0/17
    Main Route Table:
      Destination   Target
      10.0.0.0/16   local
      10.2.0.0/16   local

There are a few rules for adding a CIDR block to your VPC, see here: https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#add-cidr-block-restrictions

### Security Groups

__Security Groups__ are the firewall for AWS EC2 instances since they control both the inbound and outbound traffic
at an instance level.

There is a network specific security group called __Network Access Control Lists__ (__ACL__) that act as a firewall
for a subnet, meaning it will control inbound and outbound traffic at a subnet level.

### Flow Logs

In AWS, there are __Flow Logs__ that capture the information about the IP traffic coming and going from the network
interfaces in your VPC. These logs are published to CloudWatch Logs or S3.

### Internet Gateways

An __internet gateway__ is a VPC component that allows communication between instances in your VPC and the internet.
An internet gateway has two purposes:

* Provide a target in your VPC route tables for internet-routable traffic
* Provide network address translation (NAT) for instances that have been assigned public IPv4 addresses

### NAT (Gateway and Instance)

You can use a __NAT__ device to enable instances in a private subnet to connect to the internet while also
preventing the internet from initiating connections with the instances. There are two different types of NAT devices,
a __NAT gateway__ or a __NAT instance__.

#### NAT Gateway

A __NAT Gateway__ (aka __network address translation (NAT)__) enables instances in a private subnet to connect
to the internet or other AWS Services, but prevents the internet from initiating a connection with those other
instances.

To create a NAT gateway, we have to specify the public subnet where the NAT gateway resides as well as an
elastic IP address to associate with the NAT gateway. After you create your NAT gateway, you have to update the
route table associated with one or more of your private subnets to point Internet-bound traffic to the NAT gateway
(which will enable instances in your private subnets to communicate with the Internet).

## DNS

__Domain Name System__ (__DNS__) is how names are resolved to their corresponding IP Address.
It's made up of a __host name__ and a __domain name__.

## Network Interfaces

In AWS, there are __Elastic Network Interfaces__ (aka network interface), that is a logical netowkring component
in a VPC that represents a virtual network card. You create and configure network interfaces and attach them to
instances in your VPC.

### Requester-Managed Network Interfaces

A __requester-managed network interface__ is a network interface that an AWS service creates in your VPC.
This network interface can represent an instance for another service (e.g. an AWS RDS instance).
