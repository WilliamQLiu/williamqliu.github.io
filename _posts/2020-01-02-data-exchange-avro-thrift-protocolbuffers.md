---
layout: post
title: Data Exchange Formats (Avro, Thrift, Protocol Buffers)
---


# {{ page.title }}

There are a lot of Data Exchange formats, each with its own use-case. Some include:

* JSON
* Protocol Buffers (Protobuf)
* Thrift
* AVRO

Things to consider:

* Efficency - Time and Space
* Ease/Speed of development

E.g. JSON vs Binary; Binary is very fast time and space, but hard to develop with because it is error prone

If you're looking for a cross-language serialization of data using a schema (and requires __schema evolution__),
but not sure which one to use, then here's a comparison.

### schema evolution

If/When you change a schema, you'll have producers and consumers with different versions at the same time.
Schema evolution allows your producers and consumers to continue to work across schemas. Some concepts for
schema evolution involve forward and backward compatibility.


#### schema evolution scenarios

Scenarios for forward and backward compatibility are:

* No change in fields
* Added field, old client (producer), new server (consumer)
* Removed field, old client (producer), new server (consumer)
* Added field, new client (producer), old server (consumer)
* Removed field, new client (producer), old server (consumer)

Scenario: No change in fields - producer (client) sends a message to a consumer (server) - all good
  e.g. MyMsg            MyMsg
       user_id: 123     user_id: 123
       amount: 1000     amount: 1000

Scenario: Added field - producer (old client) sends an old message to a consumer (new server); new server recognizes that
the field is not set, and implements default behavior for out-of-date requests - all good
  e.g. MyMsg            MyMsg
       user_id: 123     user_id: 123
       amount: 1000     amount: 1000
                        time: 15

Scenario: Added field - producer (new client) sends a new message to a consumer (old server); old server simply ignores it
and processes as normal - all good
  e.g. MyMsg            MyMsg
       user_id: 123     user_id: 123
       amount: 1000     amount: 1000
       time: 15

## JSON

Slow (e.g. store field names over and over) and verbose, but very easy to use. Does not have features like
differentiating integers from floating point (i.e. inconsistent types). Flexible format is good and bad.
Not good if you want a schema and some documentation. No graceful schema evolution.

Example

    {
        "userName": "Will",
        "favoriteNumber": 13,
        "interests": ["programming", "climbing"]
    }

## Protocol Buffer (Protobuf)

Designed by Google and open-sourced since 2008, officially supports generated code in: C++, Java, Python, and Objective-C
The proto3 language version also works with Dart, Go, Ruby, and C#

Example

    syntax = "proto3"

    message Person {
        string query = 1;
        int32 page_number = 2;
        int32 result_per_age = 3;
    }

So how does this work? We define IDL Rules:

* By default the syntax is "proto2" so we have to define our syntax
* We create a message definition with three fields containing (name/value pairs),
  one for each piece of data that you want to include in the message.
* Each field in the message definition has a unique number (e.g. 1, 2, 3)
* We look at the type (e.g. a string), then a UTF-8 encoding of the string, then a variable-length encoding of the number
* It's a simple way of using a tag number to represent each field

Message Types can be:

* Scalars https://developers.google.com/protocol-buffers/docs/proto3#scalar
* Enumerations https://developers.google.com/protocol-buffers/docs/proto3#enum
* Other Messages https://developers.google.com/protocol-buffers/docs/proto3#other

Things to watch out for:

* You can rename fields (because field names don't exist in the binary serialization)
* You can never change a tag number
* It is safe to remove a field from the schema, but you must never reuse that tag number for another field in the future
  (because you might have data stored using that tag)

## Avro

Things to watch out for:

* Avro is used in Confluent's Kafka Schema Registry

## Thrift

Open sourced by Facebook in 2007. Thrift makes RPC a first class citizen (unlike Protobuf).

Bigger than Avro or Protocol Buffers, but is not just a data serialization library; it's an entire RPC framework.
Instead of using a single binary encoding (like Avro and Protobuf), Thrift has a whole variety of different
serialization formats (called __protocols__).

## Typical Operation Model

The typical model of Thrift/Protobuf is:

* Write down a bunch of struct-like message formats in an IDL-like language
* Run a tool to generate Java/C++/whatever boilerplate code
  E.g. thrift --gen java MyProject.thrift
* Outputs thousands of lines - but they remain fairly readable in most languages
* Link against this boilerplate when you build your application

## gRPC

In __gRPC__, a client application can directly call methods on a server application on a different machine as if
it was a local object, making it easier to create distributed applications and services.

The idea is to define a service, specify the methods that can be called remotely with their parameters and return types.
On the server side, the server implements this interface and runs a gRPC server to handle client calls.
On the client side, the client has a stub that provides the same methods as the server.

Basically, a gRPC client (e.g. say Ruby Client) makes a Proto Request to a gRPC Server, then gets a Proto Response.

Interface Definition Language (IDL)


