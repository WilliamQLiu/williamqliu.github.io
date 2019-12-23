---
layout: post
title: Logentries
---


# {{ page.title }}

Logentries lets you collect data from many services at once in a fairly easy to use GUI.

## Adding a Log

You can add a log using a few different methods:

* Logs and Log Sets - __Log sets__ allow you to group your logs together (e.g. by host, environment, region, role).
* Agent Based Logging - you can setup __agents__ to automate log collection in your environment
* Syslog Forwarding - Use your operating systems' log forwarder to send logs from your OS directly to Logentries
  You can configure forwarding for Linux based (Rsyslog, Syslog-D, Syslog-NG)
* Docker Logging Container - Collect system-level log data and container-level performance stats
* Application Logging - Use say Python or Ruby to do in-application logging
* Token Based Logging - Recommended method for autoscaling environments

