---
layout: post
title: Design Patterns
---


# {{ page.title }}

# Summary

The below notes are based on the book __Designs Patterns - Elements of Reusable Object-Oriented Software__.
The idea is that all well structured object-oriented architectures are full of patterns. When you design a system, you
need to focus on the common collaborations among its objects (i.e. its design pattern). The book shows you the 
importance that patterns can play in architecting complex systems, then it provides a reference of well-engineered 
patterns that a developer can apply to their own applications.

# Why Design Patterns

Designing reusable object-oriented software is hard. Recording the experience of designing object-oriented software
is known as __design patterns__, that way we can reuse successful designs and architectures. For a more specific
definition, we'll say that design patterns are `descriptions of communicating objects and classes that are customized
to solve a general design problem in a particular context`. Each design pattern identifies participating classes
and instances, their roles and collaborations, and the distribution of responsibilities.

Besides software engineering, you can see design patterns used. Playwrights don't write plots from scratch.
They normally follow a pattern like the 'Tragically Flawed Hero' or 'The Romantic Novel'. Once you know the patterns,
a lot of design decisions follow automatically.

# Essential Elements of a Design Pattern

A design pattern has four essential elements:

* the __pattern name__ is a handle we use to describe a design problem, its solutions, and consequences in a word or two
* the __problem__ describes when to apply the pattern; it explains the problem and its context
* the __solution__ describes the elements that make up the design, their relationships, responsibilities, and collaborations.
  the solution doesn't describe a particular concrete design or implementation because patterns are templates
* the __consequences__ are the results and trade-offs of applying the pattern, usually space and time trade-offs

# Types of Design Patterns

__Design Patterns__ fall into three categories:

* __Creational__
* __Structural__
* __Behavioral__

# Catalog of Design Patterns

Common design patterns include:

* __Adapter__ - convert the interface of class into another interface clients expect
* __Bridge__ - decouple an abstraction from its implementation so that the two can vary independently
* __Builder__ - Separate the construction of a complex object from its representation so that the same construction
                process can create different representations
* __Chain of Responsibility__ - Avoid coupling the sender of a request to its receiver by giving more than one object
                                a chance to handle the request. Chaint he receiving objects and pass the request along
                                the chain until an object handles it
* __Command__ - Encapsulate a request as an object, thereby letting you parameterize clients with different requests,
                queue or log request, and support undoable operations
* __Composite__ - Compose objects into tree structures to represent part-whole hierarchies. Composite lets clients
                  treat individual objects and compositions of objects uniformly
* __Decorator__ - Attach additional responsibilities to an object dynamically. Decorators provide a flexible
                  alternative to subclassing for extending functionality
* __Facade__ - provide a unified interface to a set of interfaces in a subsystem. Facade defines a higher-level
               interface that makes the subsystem easier to use
* __Factory Method__
* __Observer__
* __Strategy__
* __Template Method__

# Example Design Pattern - the MVC 

In web applications, we commonly see the __Model__/__View__/__Controller__ (__MVC__) classes used to build
user interfaces. We have:

* the __Model__ is the application object
* the __View__ is its screen presentation
* the __Controller__ defines the way the user interface reacts to user input

## MVC - Models and Views with Observer Pattern

MVC allows decoupling of views and models by establishing a __subscribe/notify__ protocol between them. A view must
ensure that its appearance reflects the state of the model. When a model's data changes, the model notifies views
that depend on it. In response, each view gets an opportunity to update itself. You can then attach multiple views
to a model to provider different presentations.

We can see this as a general pattern of decoupling views from models, or we can see this apply to a more general
problem: decoupling objects so that changes to one can affect any numer of others without requiring the changed object
to know details of the others, which is known as the __Observer Pattern__.

## MVC - Views with Composite Pattern

MVC allows views to be nested. An example might be a control panel of buttons that appears as a view containing
a lot of button views. We can think of this user interface that groups together multiple views as a Composite View,
basically treating the Composite View like one of the nested Views. This general design pattern is the
__Composite Pattern__, which lets you create a class hierarchy where some subclasses define primitive objects (e.g.
Button) and other classes define composite objects (e.g. Composite View) that assembles the primitives into more
complex objects. Basically, a group of objects is treated the same way as a single instance of the same type of object.

## MVC - Views and Controllers with Strategy Pattern

MVC's View uses an instance of the Controller subclass to implement a particular response strategy; to implement
a different strategy, you just replace the instance with a different kind of controller. For example, a view can be
disabled so that it doesn't accept any input by giving it a controller that ignores input events.

MVC has a View-Controller relationship that is an example of the __Strategy Pattern__. A Strategy is an object that
represents an algorithm. It's useful when you want to replace the algorithm either statically or dynamically,
when there's a lot of variants of the algorithm, or when the algorithm has complex data structures you want to
encapsulate.

## MVC - Views with Decorator Pattern

MVC might have a View that needs the scrolling functionality. We can use the __Decorator Pattern__ to add
scrolling capabilities to a single object.

## MVC - Controllers with Factory Pattern

MVC might have a Controller that specifies the default Controller Class for a View. We can use the __Factory Pattern__
to create objects without having to specify the exact class of the object that will be created.
