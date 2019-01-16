---
layout: post
title: Linux Tools
---


# {{ page.title }}

Linux has a lot of useful programs that's an `apt-get` away.


## iPerf

iPerf/iPerf3 is a tool for checking active measurements of the maximum achievable bandwidth on IP networks.
You need to install iperf on the computers at both ends of the connection you're testing. One system acts as the
server while the other acts as a client. The client connects to the server you're testing the speed of.

1. Launch iperf in server mode on the server you plan to test with `iperf -s`
2. On your second server, connect to the first server with `iperf -c 198.51.100.5` (replace the ip address)

You can also test for bidirectional throughput testing with `iperf -c 198.51.100.5 -d` (replace the ip address)

## iftop

iftop shows network usage like what top does for CPU usage. iftop listens to network traffic on a named interface
and displays a table of current bandwidth usage by pairs of hosts. Basically, whose using up all the bandwidth and
what are they doing?!

Use `sudo ifconfig` OR `sudo ip addr show` to find all attached network interfaces on your Linux system.
Then use the `-i` flag to specify the interface you want to monitor, e.g. `sudo iftop -i wlp58s0`

    $sudo ip addr show
    interface: wlp58s0
    IP address is: 192.168.86.21
    MAC address is: 9c:b6:d0:e0:02:ba

    $sudo iftop -i wlp58s0
                            1.91Mb                      3.81Mb                      5.72Mb                      7.63Mb                9.54Mb
    └───────────────────────────┴───────────────────────────┴───────────────────────────┴───────────────────────────┴───────────────────────────
    will-xps.lan                                             => 209.85.164.187                                           83.5Kb  16.7Kb  4.18Kb
                                                             <=                                                          5.25Mb  1.05Mb   269Kb
    will-xps.lan                                             => 173.194.162.170                                          24.9Kb  4.97Kb  1.24Kb
                                                             <=                                                           597Kb   119Kb  29.8Kb
    will-xps.lan                                             => den02s02-in-f14.1e100.net                                 110Kb  26.7Kb  8.82Kb
                                                             <=                                                           210Kb  78.7Kb  21.0Kb
    will-xps.lan                                             => lga15s42-in-f22.1e100.net                                9.79Kb  2.39Kb   815b
                                                             <=                                                           144Kb  33.2Kb  11.4Kb
    will-xps.lan                                             => lga15s45-in-f1.1e100.net                                 11.7Kb  2.34Kb   599b
                                                             <=                                                           126Kb  25.3Kb  6.32Kb
    will-xps.lan                                             => 173.194.162.171                                          30.2Kb  6.04Kb  1.51Kb
                                                             <=                                                          62.6Kb  12.5Kb  3.13Kb
    will-xps.lan                                             => 209.85.164.184                                           19.5Kb  3.90Kb   998b
                                                             <=                                                          64.5Kb  12.9Kb  3.22Kb

The last three columns show the average bandwidth the last 2, 10, and 40 seconds

`TX` stands for Overall Transmitted Traffic
`RX` stands for overall received traffic

## nslookup

Nslookup is a program to query Internet domain name servers.

    $nslookup -type=any google.com
    nslookup -type=any google.com
    Server:     192.168.1.8
    Address:    192.168.1.8#53

    Non-authoritative answer:
    Name:   google.com
    Address: 172.217.12.14
    google.com  nameserver = ns2.google.com.
    google.com
        origin = ns1.google.com
        mail addr = dns-admin.google.com
        serial = 229344848
        refresh = 900
        retry = 900
        expire = 1800
        minimum = 60
    google.com  mail exchanger = 20 alt1.aspmx.l.google.com.
    google.com  text = "globalsign-smime-dv=CDYX+XFHUw2wml6/Gb8+59BsH31KzUr6c1l2BPvqKX8="
    google.com  has AAAA address 2607:f8b0:400f:801::200e
    google.com  rdata_257 = 0 issue "pki.goog"

    Authoritative answers can be found from:
    ns2.google.com  internet address = 216.239.34.10
    alt1.aspmx.l.google.com internet address = 64.233.185.27


