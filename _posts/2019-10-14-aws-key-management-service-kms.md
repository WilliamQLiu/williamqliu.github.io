---
layout: post
title: AWS Key Management Service (KMS)
---


# {{ page.title }}

## Summary

__AWS Key Management Service (KMS)__ lets you create and manage keys needed for encryption across AWS services.
AWS KMS is a single control point for managing your keys and policies.

## KMS with IAM

AWS KMS works closely with AWS IAM to create a KMS Key. The KMS Key can have Key Policies attached, which say
that user-1 or user-2 has access or are denied to resources.

