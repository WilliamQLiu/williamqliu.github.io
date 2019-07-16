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

###


