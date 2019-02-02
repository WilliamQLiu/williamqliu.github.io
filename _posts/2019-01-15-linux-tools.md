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

Install with:

    pip install glances

    Run with `glances`

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


## glances

__Glances__ is a monitoring tool similar to htop, but with some additional information more focused on I/O.
For example:


    CPU  [||||||||||||||||||||||                                                    29.9%]   CPU /    29.9%  nice:     0.0%  ctx_sw:    5K      MEM -   56.1%  active:    5.49G      SWAP -    0.0%      LOAD    4-core
    MEM  [|||||||||||||||||||||||||||||||||||||||||                                 56.1%]   user:    24.6%  irq:      0.0%  inter:   7309      total:  7.68G  inactive:  1.14G      total:   15.8G      1 min:    1.34
    SWAP [                                                                           0.0%]   system:   5.1%  iowait:   0.4%  sw_int:   899      used:   4.31G  buffers:   1.67G      used:        0      5 min:    1.18
                                                                                             idle:    69.7%  steal:    0.0%                     free:   3.37G  cached:    1.86G      free:    15.8G      15 min:   1.08

    NETWORK     Rx/s   Tx/s   TASKS 270 (1080 thr), 1 run, 199 slp, 70 oth sorted automatically by cpu_percent, flat view
    docker0       0b     0b
    lo          232b   232b   Systemd          7    Services loaded: 192 active: 192 
    wlp58s0       0b     0b
                                CPU%  MEM%  VIRT   RES   PID USER        NI S     TIME+   R/s   W/s Command 
    DefaultGateway      7ms     42.4   4.6 1.05G  362M 18172 will         0 S  25:57.51     0     0 /opt/google/chrome/chrome
                                30.8   0.5  477M 39.1M 30574 will         0 R   0:18.26     0     0 /usr/bin/python3 /usr/bin/glances
    DISK I/O     R/s    W/s     11.9   5.4 2.06G  422M 15716 will         0 S   1h49:46     0   12K /opt/google/chrome/chrome
    nvme0n1       4K    62K     11.6   2.6  835M  201M 28863 will         0 S   1:21.24     0     0 /opt/google/chrome/chrome
    nvme0n1p1      0      0      8.7   0.1 1.09G 11.7M  4310 will       -11 S  17:42.90     0     0 /usr/bin/pulseaudio --start --log-target=syslog
    nvme0n1p2      0      0      7.5   1.0  735M 78.2M 29040 will         0 S   8:32.93    4K     0 /usr/bin/python /usr/bin/x-terminal-emulator
    nvme0n1p3     4K    62K      6.6   0.8  355M 59.0M  4083 will         0 S  33:54.50     0     0 /usr/lib/xorg/Xorg vt2 -displayfd 3 -auth /run/user/1001/gdm/Xauthority -background none -noreset -keeptty -verbose
    nvme0n1p4      0      0      3.0   1.3  749M  101M 31033 will         0 S   0:01.93     0     0 /opt/google/chrome/chrome
                                 2.7   1.1  687M 83.9M 21727 will         0 S   2:01.23     0     0 /opt/google/chrome/chrome
    FILE SYS    Used  Total      1.8   2.6  857M  206M 21683 will         0 S   1:36.43     0     0 /opt/google/chrome/chrome
    /           131G   216G      1.2   0.0     0     0   422 root         0 S   5:37.87     0     0 irq/51-DLL075B:
    /boot/efi  6.09M   238M      1.2   4.0 1.09G  314M 21963 will         0 S   2:27.74     0     0 /opt/google/chrome/chrome
    _ore/5897  88.2M  88.2M      1.2   4.1 1.14G  319M 22275 will         0 S   1:21.57     0     0 /opt/google/chrome/chrome
    _ore/6034  89.5M  89.5M      0.6   2.1  832M  167M 21894 will         0 S   2:00.31     0     0 /opt/google/chrome/chrome
    _ore/6130  89.5M  89.5M      0.3   0.1  220M 9.16M     1 root         0 S   7h02:27     0     0 /sbin/init splash
                                 0.3   0.0     0     0     8 root         0 ?   1:16.70     0     0 rcu_sched
    SENSORS                      0.3   3.0  991M  233M 17427 will         0 S   7:24.96     0     0 /opt/google/chrome/chrome
    acpitz 1            25C      0.3   0.0     0     0 28963 root         0 ?   0:00.14     0     0 kworker/3:2
    pch_skylake 1       39C      0.3   0.0     0     0 29737 root         0 ?   0:00.11     0     0 kworker/1:5
    Package id 0        48C      0.3   1.8  767M  139M 30579 will         0 S   0:24.82     0     0 /opt/google/chrome/chrome
    Core 0              44C      0.0   0.0     0     0     2 root         0 S   0:00.10     0     0 kthreadd
    Core 1              48C      0.0   0.0     0     0     4 root       -20 ?   0:00.00     0     0 kworker/0:0H
    Battery             86%      0.0   0.0     0     0     6 root       -20 ?   0:00.00     0     0 mm_percpu_wq
                                 0.0   0.0     0     0     7 root         0 S   0:07.62     0     0 ksoftirqd/0
    
