---
layout: post
title: Kubernetes in Action
---


# {{ page.title }}

## Issue: How do you isolate applications

Software components running on the same machine will require different, often conflicting
versions of libraries. We can solve this issue by using virtual machines and containers
to isolate environments.


## Virtual Machines vs Containers

__Virtual Machines__ (VMs) is when applications are isolated with their own guest operating system. This means a VM is used when there are a few applications (e.g. App A, App B) running on the same environment (i.e. on a Guest OS). If you have more applications that are small, then giving each application their own VM is a waste of hardware.

So what does this look like? Say you have three VMs (VM1, VM2, VM3) and each VM is running two Apps,
each on a Guest OS of their own. All three of the VMs would be running underneath on a bare-metal machine that has its own (single) Host OS and Hypervisor.

### Hypervisors

The **Hypervisor** divides the physical hardware resources into smaller sets of virtual resources that
can be used by the operating system inside each VM. The applications running inside the VMs can do
system calls to the guest OS' kernel in the VM, and then the kernel then performs x86 instructions
on the virtual CPU, which are sent through the hypervisor to the host's physical CPU.

There are two types of hypervisors:

* hypervisors that do not use a host OS
* hypervisors that do use a host OS


