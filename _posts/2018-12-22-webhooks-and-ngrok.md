---
layout: post
title: Webhooks (and ngrok)
---


# {{ page.title }}

## Summary

__Webhooks__ are "user-defined HTTP callbacks" (aka __Reverse APIs__).

In a normal API (Application Programmer Interface), you make a request.

1. make a request (e.g. GET or POST to a url)
2. get a response

The reason why webhooks are sometimes known as reverse APIs is because it lets you skip a step. You don't even have
to make a request anymore. The webhook basically lets you just:

1. get a response

### Example Webhook Scenario

Say you're watching when a change is made to a project on GitHub. You can keep making a request every say 10 minutes
and check the difference from the last response.

However, if you have a webhook setup, you can add it so that when there are changes made to a project on GitHub,
the server sends you a specific response.

Other examples include:

* getting notifications when say an email bounces
* getting notifications when a recipient clicks a link
* getting notifications when a file has changed

### Example Payload

An example delivery might look like:

    POST /payload HTTP/1.1
    Host: localhost:4567
    X-Github-Delivery: 72d3162e-cc78-11e3-81ab-4c9367dc0958
    X-Hub-Signature: sha1=7d38cdd689735b008b3c702edd92eea23791c5f6
    User-Agent: GitHub-Hookshot/044aadd
    Content-Type: application/json
    Content-Length: 6615
    X-GitHub-Event: issues
    {
      "action": "opened",
      "issue": {
        "url": "https://api.github.com/repos/octocat/Hello-World/issues/1347",
        "number": 1347,
        ...
      },
      "repository" : {
        "id": 1296269,
        "full_name": "octocat/Hello-World",
        "owner": {
          "login": "octocat",
          "id": 1,
          ...
        },
        ...
      },
      "sender": {
        "login": "octocat",
        "id": 1,
        ...
      }
    }

## Where to send response

So you normally have a webserver with an API exposed that can receive these responses. Example webserver frameworks
might be Flask or Django. You can run it locally or deploy it somewhere. If you do run it locally, the question is
how do you get that exposed to the outside world so you can test your webhook?

## ngrok

__ngrok__ is a site for taking your local webserver and creating a tunnel out. That way your `localhost:4000`
might turn out to be `https://willsapp.ngrok.io`

