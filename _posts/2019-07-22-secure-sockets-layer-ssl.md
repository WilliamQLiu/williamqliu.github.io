---
layout: post
title: Secure Sockets Layer (SSL) and TLS
---


# {{ page.title }}

__SSL__ and __TLS__ are both cryptographic protocols that provide authentication and data encryption between
servers. SSL and TLS are how we secure network communications using encryption.

__SSL__ stands for __Secure Socket Layer__
__TLS__ stands for __Transport Layer Security__ - supercedes SSL

## What is a Protocol

So what is a __protocol__? It's the official procedure or system of rules.

It's like we all agree that there are only 24 hours in a day, a week is 7 days, etc. even though we track
time differently.

## Common Protocols

If we want to connect to a web site from a client browser, we'll use __HTTP__ (Hypertext Transfer Protocol)
If we want to transfer large files, we'll use a __FTP__ (File Transfer Protocol) over a TCP/IP based network connection.
If we want to send email, we use SimpleMail Transfer Protocol (__SMTP__)

## Secure Protocols

__SSH__ allows remote login and execution. It is a __cryptographic network protocol__ used for secure data communication.
It is secured through the use of generated keys out of band (i.e. shared across a public median).

__SSL__ stands for __Secure Socket Layer__ (__SSL__) and is superceded by __TLS__. The terms are sometimes used
interchangeably. The idea is that data is scrambled in transit so attackers can't read it. Our SSL Wrapper is
a little different than HTTP, FTP, SMTP, which works on the __Application Layer__ of the __OSI Model__.
Instead, our SSL wrapper works on the __Transport Layer__ of the OSI Model.

## OSI Model

__OSI model__ stands for the __Open Systems Interconnection model__, which is made up of:

7 Application
6 Presentation
5 Session
4 Transport
3 Network
2 Data Link
1 Physical

Detailed breakdown of the OSI model layers:

Layer                               Protocol data unit (PDU)    Function
Host layers     7    Application    Data                        High-level APIs, including resource sharing, remote file access
                6    Presentation   Data                        Translation of data between a networking service and an application; including character encoding, data compression and encryption/decryption
                5    Session        Data                        Managing communication sessions, i.e. continuous exchange of information in the form of multiple back-and-forth transmissions between two nodes
                4    Transport      Segment, Datagram           Reliable transmission of data segments between points on a network, including segmentation, acknowledgement and multiplexing
Media layers    3    Network        Packet                      Structuring and managing a multi-node network, including addressing, routing and traffic control
                2    Data link      Frame                       Reliable transmission of data frames between two nodes connected by a physical layer
                1    Physical       Symbol                      Transmission and reception of raw bit streams over a physical medium

## Introduction to Cryptography

__Cryptography__ is the computerized __enciphering__ and __deciphering__ of information.
As we pass messages back and forth between our network (in transit), we do not want data to be stolen or altered.
Cryptography stands for 'kryptos' (hidden) and 'graphein' (writing), meaning hidden writing. We're hiding data
that we're writing.

## How does encryption work?

Let's think of a lock and a key. The lock is used to keep unauthorized people out.
The key is used to both close and open that lock. With computer systems, we do not want to reuse the
same key over and over, otherwise that key might be stolen.

## Why do we use Cryptography?

We use cryptography so that we can have:

* __Confidentiality__ - what should be private stays private
* __Integrity__ - Data remains unaltered except by authorized entities
* __Authenticity__ - Assurance that the message came from a verified source
* __Non-Repudiation__ - Integrity and Authenticity gives us Non-Repudiation. Used in transactional exchanges to
  assure both the sender and receiver of the other's status in the exchange. Did data come from the source it says
  it should come from and is it unaltered?
* __Obfuscation__ - Making something obscure, unclear, or unintelligible

## Encryption Types

### Symmetric Encryption

__Symmetric__ encryption uses the __same key__ for both encryption and decryption. The issue with symmetric
encryption is that keys have to be shared between parties, usually across a public medium. If this key is
intercepted during transit, then security is compromised.

Symmetric Algorithms include:

* DES
* 3DES
* AES (Rijndael)
* Blowfish

### Asymmetric Encryption

__Asymmetric__ encryption uses __two keys__ for encryption and decryption.

Asymmetric Algorithms include:

* Diffie-Hellman
* RSA
* ECC

#### Diffie-Hellman

__Diffie-Hellman Key Exchange__ is the first asymmetric key exchange. It was built to fix the issues that
a symmetric encryption had. To facilitate this, both users in an exchange agree on a shared private key.
Say my key is 300 and your key is 200, our shared key could be 300 * 200 = 60000. At least that is the basic idea.

#### RSA

__RSA__ is one of the most widely encryptions used for secure data transmission.
In RSA, each user has a widely available public key as well as a secret private key.
When sending a message, the sender uses the receiver's public key to encrypt that message.
The only key that can be used to decrypt that message is the receiver's private key.

