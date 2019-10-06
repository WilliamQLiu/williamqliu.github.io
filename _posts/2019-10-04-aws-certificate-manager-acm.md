---
layout: post
title: AWS Certificate Manager (ACM)
---


# {{ page.title }}

__AWS Certificate Manager (ACM)__ service handles the complexity of creating and managing SSL/TLS certificates
for your AWS based websites and applications. You can:

* Use ACM for public certificates (ACM certificates)
* Import a certificate into ACM
* Use ACM to create wildcard SSL certificates (to support multiple domain names)
* Use ACM to issue private certificates (by using ACM PCA)

## When to use ACM

If you are in a region that AWS supports ACM, then use ACM to provision, manage, and deploy your server
certificates. In unsupported regions, you have to use IAM as a certificate manager.

## ACM PCA

ACM is tightly linked with __AWS Certificate Manager Private Certificate Authority__ (__ACM PCA__).
You can use ACM PCA to create a private certificate authority (CA).

## Certificate Authority

A __certificate authority (CA)__ is an entity that issues digital certificates. The most common type of digital
certificate is based on the `ISO X.509` standard. The CA issues signed digital certificates that affirm the identity
of the certificate subject and bind that identity to the public key contained in the certificate. A CA can also
manage certificate revocation.

## Domain Name System

The __Domain Name System (DNS)__ is a hierarchical distributed naming system for computers and other resources
connected to the internet or a private network. DNS is usually used to translate text domain names (e.g.
`aws.amazon.com` into numerical IP (Internet Protocol) addresses (e.g. `111.122.133.144`).

## Domain Names

A __domain name__ is a text string (e.g. `www.example.com`) that can be translated by the Domain Name System into
an IP Address. A domain name is made up of distinct labels separated by periods.

### TLD

The rightmost label is the __top-level domain (TLD)__ (e.g. `.com`, `.net`, `.edu`).
Sometimes you'll see a __country code__ as well (e.g. `.uk` for the United Kingdom, `.ru` for Russia); when there
is a country code, a second-level hierarchy for the TLD is introduced (e.g. `.co.uk` TLD identifies commercial
enterpries in the United Kingdom).

### Apex domain

### Subdomain

### FQDN

### PQDN

## Example ACM Certificate

ACM generates `X.509 version 3` certificates. Each is valid for 13 months. An example certificate might look like:

    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                f2:16:ad:85:d8:42:d1:8a:3f:33:fa:cc:c8:50:a8:9e
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: O=Example CA
            Validity
                Not Before: Jan 30 18:46:53 2018 GMT
                Not After : Jan 31 19:46:53 2018 GMT
            Subject: C=US, ST=VA, L=Herndon, O=Amazon, OU=AWS, CN=example.com
            Subject Public Key Info:
                Public Key Algorithm: rsaEncryption
                    Public-Key: (2048 bit)
                    Modulus:
                        00:ba:a6:8a:aa:91:0b:63:e8:08:de:ca:e7:59:a4:
                        69:4c:e9:ea:26:04:d5:31:54:f5:ec:cb:4e:af:27:
                        e3:94:0f:a6:85:41:6b:8e:a3:c1:c8:c0:3f:1c:ac:
                        a2:ca:0a:b2:dd:7f:c0:57:53:0b:9f:b4:70:78:d5:
                        43:20:ef:2c:07:5a:e4:1f:d1:25:24:4a:81:ab:d5:
                        08:26:73:f8:a6:d7:22:c2:4f:4f:86:72:0e:11:95:
                        03:96:6d:d5:3f:ff:18:a6:0b:36:c5:4f:78:bc:51:
                        b5:b6:36:86:7c:36:65:6f:2e:82:73:1f:c7:95:85:
                        a4:77:96:3f:c0:96:e2:02:94:64:f0:3a:df:e0:76:
                        05:c4:56:a2:44:72:6f:8a:8a:a1:f3:ee:34:47:14:
                        bc:32:f7:50:6a:e9:42:f5:f4:1c:9a:7a:74:1d:e5:
                        68:09:75:19:4b:ac:c6:33:90:97:8c:0d:d1:eb:8a:
                        02:f3:3e:01:83:8d:16:f6:40:39:21:be:1a:72:d8:
                        5a:15:68:75:42:3e:f0:0d:54:16:ed:9a:8f:94:ec:
                        59:25:e0:37:8e:af:6a:6d:99:0a:8d:7d:78:0f:ea:
                        40:6d:3a:55:36:8e:60:5b:d6:0d:b4:06:a3:ac:ab:
                        e2:bf:c9:b7:fe:22:9e:2a:f6:f3:42:bb:94:3e:b7:
                        08:73
                    Exponent: 65537 (0x10001)
            X509v3 extensions:
                X509v3 Basic Constraints:
                    CA:FALSE
                X509v3 Authority Key Identifier:
                    keyid:84:8C:AC:03:A2:38:D9:B6:81:7C:DF:F1:95:C3:28:31:D5:F7:88:42
                X509v3 Subject Key Identifier:
                    97:06:15:F1:EA:EC:07:83:4C:19:A9:2F:AF:BA:BB:FC:B2:3B:55:D8
                X509v3 Key Usage: critical
                    Digital Signature, Key Encipherment
                X509v3 Extended Key Usage:
                    TLS Web Server Authentication, TLS Web Client Authentication
                X509v3 CRL Distribution Points:
                    Full Name:
                      URI:http://example.com/crl

        Signature Algorithm: sha256WithRSAEncryption
             69:03:15:0c:fb:a9:39:a3:30:63:b2:d4:fb:cc:8f:48:a3:46:
             69:60:a7:33:4a:f4:74:88:c6:b6:b6:b8:ab:32:c2:a0:98:c6:
             8d:f0:8f:b5:df:78:a1:5b:02:18:72:65:bb:53:af:2f:3a:43:
             76:3c:9d:d4:35:a2:e2:1f:29:11:67:80:29:b9:fe:c9:42:52:
             cb:6d:cd:d0:e2:2f:16:26:19:cd:f7:26:c5:dc:81:40:3b:e3:
             d1:b0:7e:ba:80:99:9a:5f:dd:92:b0:bb:0c:32:dd:68:69:08:
             e9:3c:41:2f:15:a7:53:78:4d:33:45:17:3e:f2:f1:45:6b:e7:
             17:d4:80:41:15:75:ed:c3:d4:b5:e3:48:8d:b5:0d:86:d4:7d:
             94:27:62:84:d8:98:6f:90:1e:9c:e0:0b:fa:94:cc:9c:ee:3a:
             8a:6e:6a:9d:ad:b8:76:7b:9a:5f:d1:a5:4f:d0:b7:07:f8:1c:
             03:e5:3a:90:8c:bc:76:c9:96:f0:4a:31:65:60:d8:10:fc:36:
             44:8a:c1:fb:9c:33:75:fe:a6:08:d3:89:81:b0:6f:c3:04:0b:
             a3:04:a1:d1:1c:46:57:41:08:40:b1:38:f9:57:62:97:10:42:
             8e:f3:a7:a8:77:26:71:74:c2:0a:5b:9e:cc:d5:2c:c5:27:c3:
             12:b9:35:d5


