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

What is a virtual machine? __Virtual Machines__ (VMs) is when applications are isolated with their own guest operating system. This means a VM is used when there are a few applications (e.g. App A, App B) running on the same environment (i.e. on a Guest OS). If you have more applications that are small, then giving each application their own VM is a waste of hardware. The main benefit of VMs is that they have full isolation (each VM
has its own Linux kernel).

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

## Containers

What is a container? **Containers** have all system calls run on the same sinngle kernel that are then run on the host physical CPU.
Use containers when you have a large number of processes that you want to isolate since containers have
very little overhead (e.g. does not need to run a virtual OS so it does not have the overhead of
needing to run a VM set of system services, only the host OS's set of system services). Since you do
not need to run a VM OS, you also do not have a bootup like a VM; the process in a container starts up
immediately.

### Linux Namespaces and Linux Control Groups (cgroups)

So how does a container work? The processes are isolated, but how does it do that if everything is running
on the same operating system? The answer is Linux Namespaces and Linux Control Groups (cgroups).

* **Linux Namespaces** ensure each process sees its own personal view of the system (file, processes, network interfaces, hostname, etc).
* **Linux Control Groups (groups)** limit the amount of resources the process can consume (CPU, memory, network bandwidth, etc)


Linux Namespaces - By default, each Linux system initially has one single namespace.
All system resources (e.g. filesystems, process IDs, user IDs, network interfaces) belong to this 
single namespace. You can create additional namespaces. Other kinds of namespaces exist like:

* Mount (mnt)
* Process ID (pid)
* Network (net)
* Inter-process communication (ipc)
* UTS
* User ID (user)

Each namespace kind is used to isolate a certain group of resources. For example, the UTS namespace
determines what hostname and domain name the process running inside that namespaces sees. A process
might be assigned a specific namespace (e.g. thinks its seeing X hostname and domain name) while
another process might be assigned another namespace (e.g. think its seeing Y hostname and domain name).

Linux Control Groups (cgroups) - cgroups are a Linux kernel feature that limits the resource usage of
a process (or a group of processes).

### Docker container platform

**Docker** is a container system that makes containers easy to move across different machines.
You package up the application, its libraries and other dependencies and even the whole OS file system
into a simple package that can be used on any other machine also running Docker.
We basically get a lot of the same isolation levels that VMs do. Docker is made up of:

* **Images** - You package your application and its environment into an image. This has the filesystem
  that will be available to the application and the path to the executable when the image is run.
  Images are usually built on layers (e.g. a layer might be an ubuntu image, a python image)
* **Registries** - a Docker Registry is a repo for your Docker images; like GitHub, but for images
  instead of code. You can share (push and pull) other images out there.
* **Containers** - a Docker based container is a regular Linux container created from a docker-based
  container image. A running container is a process that runs on the host running Docker, but is
  completely isolated from both the host and all other processes running on it.

### rkt container platform

**rkt** is another Linux container engine (similar to Docker). The Open Container Initiative (OCI)
created a container image format that rkt follows. Kubernetes can also run rkt (not just Docker).


