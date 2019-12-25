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

__Design Patterns__ fall into three categories.

* __Creational__
* __Structural__
* __Behavioral__

The above categories are classified based off two criteria:

1.) __Purpose__ reflects what a pattern does

Creational patterns are about object creation.
Structural patterns deal with the composition of classes or objects
Behavorial patterns characterize the ways in which classes or objects interact and distribute responsibility

2.) __Scope__ specifies whether the pattern applies primarily to __classes__ or to __objects__.
For Python, everything is an object while classes are essentially a template to create your objects

__Class patterns__ deal with relationships between classes and their subclasses; these relationships are established
through inheritance, so they are static - fixed at compile-time.

__Object patterns__ deal with object relationships, which can be changed at run-time and are more dynamic.

Almost all patterns use inheritance so some extent so the only patterns labeled 'class patterns' are those that
focus on class relationships. Most patterns are thus labeled in the Object patterns.

                     -------------------------------------------------------------------
                     |                        Purpose                                  |
                     -------------------------------------------------------------------
                     |   Creational      |   Structural      |   Behaviroal            |
    ------------------------------------------------------------------------------------
    |Scope |  Class  |   Factory Method  |  Adapter (class)  | Interpreter             |
    |      |         |                   |                   | Template Method         |
    |      -----------------------------------------------------------------------------
    |      |  Object |  Abstract Factory |  Adapter (object) | Chain of Responsibility |
    |      |         |  Builder          |  Bridge           | Command                 |
    |      |         |  Prototype        |  Composite        | Iterator                |
    |      |         |  Singleton        |  Decorator        | Mediator                |
    |      |         |                   |  Facade           | Memento                 |
    |      |         |                   |  Flyweight        | Observer                |
    |      |         |                   |  Proxy            | State                   |
    |      |         |                   |                   | Strategy                |
    |      |         |                   |                   | Visitor                 |
    ----------------------------------------------------------------------------------

# Catalog of Design Patterns

Common design patterns include:

* __Abstract Factory__ - provide an interface for creating families of related or dependent objects without specifying
                         their concrete classes
* __Adapter__ - (aka __wrapper__) convert the interface of class into another interface clients expect
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
* __Factory Method__ - define an interface for creating an object, but let subclasses decided which class to instantiate
* __Flyweight__ - use sharing to support large numbers of fine-grained objects efficiently
* __Interpreter__ - given a language, define a representation for its grammar along with an interpreter that uses
                    the representation to interpret sentences in the language
* __Iterator__ - provide a way to access the elements of an aggregate object sequentially without exposing its underlying representation
* __Mediator__ - define an object that encapsulates how a set of objects interact; allows loose coupling by keeping
                 objects from referring to each other explicitly, and it lets you vary their interaction independently
* __Memento__ - Without violating encapsulation, capture and externalize an object's internal state so that the object
                can be restored to this state later
* __Observer__ - define a one-to-many dependency between objects so that when one object changes state, all its
                 dependents are notified and updated automatically
* __Prototype__ - specify the kinds of objects to create using a prototypical instance, and create new objects by
                  copying this prototype
* __Proxy__ - provide a surrogate or placeholder for another object to control access to it
* __Singleton__ - ensure a class only has one instance, and provide a global point of access to it
* __State__ - allow an object to alter its behavior when its internal state changes. The object will appear to change its class
* __Strategy__ - define a family of algorithms, encapsulate each one, and make them interchangeable. Strategy lets the
                 algorithm vary independently from clients that use it
* __Template Method__ - define the skeleton of an algorithm in an operation, deferring some steps to subclasses. Template
                        Method lets subclasses redefine certain steps of an algorithm without changing the algorithm's structure
* __Visitor__ - represent an operation to be performed on the elements of an object structure. Visitor lets you define
                a new operation without changing the classes of the elements on which it operates

## Simplest and Most Common Patterns:

Out of the above, the simplest and most common patterns are:

* Abstract Factory
* Adapter (aka wrapper)
* Composite
* Decorator
* Factory Method
* Observer
* Strategy
* Template Method

### Additional Patterns

* Active Record Pattern - e.g. Django's ORM - The interface of an object conforming to this pattern would include
  functions such as Insert, Update, Delete, plus properties that correspond more or less directly to the columns
  of the underlying database table. Active Record pattern is an approach to accessing data in a database


## Creational Patterns

Creational design patterns abstract the instantiation process.
Why is this important? As systems evolve, they depend more on object composition than class inheritance.
That means when you crate objects with specific behaviors, it requires more than just instantiating a class.

### Maze Example

The below five creational patterns are closely related; let's see what this means for building a maze for
a computer game. Let's assume a maze is a set of rooms and the maze knows its neighbors, which are represented
by classes for `Room`, `Door`, and `Wall` (which has a common abstract class of `MapSite`).

So let's say we define a `MazeGame` class that creates the maze. We can setup this maze a few ways:

* We can hard code the maze layout (e.g. create a new maze, set rooms, doors, then add walls). However, this
  solution is inflexible and the design is not flexible.

But what if we need to create an `EnchantedMaze` class that has new components like `DoorNeedingSpell`
or when we call the funciton to add a room, we now need an `EnchantedRoom` instead of a regular `Room`?
The creational patterns provide different ways to remove explicit references to concrete classes from
code that needs to instantiate them.

* __Abstract Factory__ - if `CreateMaze` __passed an object as a parameter__ to use to create rooms, then
  you can change the classes of rooms by passing a different parameter
* __Builder__ - if `CreateMaze` is passed an object that can create a new maze in its entirety using
  operations for adding rooms to the maze it builds, then you can use __inheritance__ to change parts of the
  maze
* __Factory Method__ - if `CreateMaze` calls __virtual functions__ instead of constructor calls to create
  the rooms, then you can change the classes that get instantiated by making a subclass of `MazeGame`
  and redefining those virtual functions
* __Prototype__ - if `CreateMaze` is parameterized by various prototypical room objects, which it then copies
  and adds to the maze, then you can change the maze by replacing these prototypical objects with a different one
* __Singleton__ - there can only be one maze per game and all game objects have ready access to it (without
  resorting to global variables or functions).

### Abstract Factory (aka Kit)

Intent: Provide an interface for creating families of related or dependent objects without specifying
        their concrete classes

Motivation: An example of an Abstract Factory would be a user interface toolkit that supports multiple
            look =-and-feel standards. You can define different look-and-feel appearances and behaviors
            for various user interface 'widgets' like scroll bars, windows, and buttons.

Solution: We define an abstract `WidgetFactory` class that declares an interface for creating each basic
          kind of widget.

### Builder

### Factory Method

### Prototype

### Singleton

## Structural Patterns

__Structural Patterns__ are concerned with how classes and objects are composed to form larger
structures. Structural class patterns use inheritance to compose interfaces or implementations.

Think how multiple inheritance mixes two or more classes into one. The result is a class that
combines the properties of its parent class. This might be useful for making independently
developed class libraries to work together. An example code snippet in Python might look like:

    class Subclass(BaseClass1, BaseClass2, BaseClass3, ...):
        pass

Another example is the __Adapter__ class. The adapter makes one interface (the adaptee's) conform
to another, providing a uniform abstraction of different interfaces. Say you have "x" and
you need "y", then Adapter solves this problem. There are a number of ways to accomplish it,
but here is the general idea with an example code snippet:

    class WhatIHave:
        def g(self):    pass
        def h(self):    pass

    class WhatIWant:
        def f(self):    pass

    class MyAdapter(WhatIwant):
        def __init__(self, whatIHave):
            self.whatIHave = whatIHave

        def f(self):
            # implement behavior using methods in WhatIHave
            self.whatIHave.g()
            self.whatIHave.h()

    class WhatIUse:
        def op(self, whatIWant):
            whatIWant.f()

Another example of a structural pattern is __Facade__, where we apply the rule
"If something is ugly, hide it inside an object". Usually this is implemented as a singleton
abstract factory. You might use this if you have a confusing collection of classes and interactions that
the client programmer doesn't really need to see, you can create an interface that is useful
for the client programmer and that only presents what's necessary.

## Behavioral Patterns

## Architectual Patterns and Styles

The above 'Creational', 'Structural', 'Behaviorla' are design patterns from 'The Gang of Four'.
There's also a few architectural patterns and styles:

### Architectural Patterns

* Three-tier
* Multilayerd architecture
* Model-View-Controller
* Domain Driven Design
* Micro-kernel
* Blackboard pattern
* Sensor-controller-actutator
* Presentation-abstraction-control

## Architectural Styles

* Structure
  - Component-based
  - Monolithic application
  - Layered
  - Pipes and filters
* Shared Memory
  - Database-centric
  - Blackboard
  - Rule-based
* Messaging
  - Event-driven (aka implicit invocation)
  - Publish-subscribe
  - Asynchronous messaging
* Adaptive Systems
  - Plug-ins
  - Microkernel
  - Reflection
  - Domain specific languages
* Distributed Systems
  - Client-server (2-tier, 3-tier, n-tier exhibit this style)
  - Shared nothing architecture
  - Space-based architecture
  - Object request broker
  - Peer-to-peer
  - Representational state transfer (REST)
  - Service-oriented (aka Microservices)
  - Cloud computing patterns

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

# How to Select a Design Pattern

Here's several approaches to finding the design pattern that's right for your problem:

* Consider how design patterns solve design problems
* Scan Intent for all the patterns
* Study how patterns interrelate - see relationships between design patterns to find the right pattern/group of patterns
* Study patterns of like-purpose - see similarities and differences between patterns of like purpose
* Examine a cause of redesign - look at the causes of redesign to see if your problem involves one or more of them, then
                                look at the patterns that help you avoid the causes of redesign
* Consider what should be variable in your design - instead of focusing on the the cause of redesign, we consider what
                                might force a change to a design (i.e. what can you change without a redesign)

# Object-Oriented Programs

So let's take a step back and look at what makes up object-oriented programs: __objects__.

## What makes up an object?
An object has both data and the procedures that operate on that data.

* Data
* Procedures - (aka __methods__ or __operations__) operate on the object data; performs an operation when it
               receives a __request__ (or __message__) from a client

## Calling Procedures with Requests

Requests are the _only way to get an object to execute an operation_. Operations are the _only way to change an
object's internal state_. Because of these restrictions, the object's internal state is said to __encapsulated__;
it cannot be accessed directly and its representation is invisible from outside the project.

## Object-oriented design methodologies

You can do object-oriented design a few different ways:

* Write a problem statement, single out the nouns and verbs, and create corresponding classes and operations
* Or focus on the collaborations and responsibilities in your system
* Or you can model the real world and translate the objects found during analysis into design

## Object Interfaces

Objects are known only through their __interfaces__. So what's an interface?

Every operation declared by an object specifies the operation's name, the objects it takes as parameters, and
the operation's return value (aka the operator's __signature__). The set of all signatures defined by an object's
operations is called the __interface__ to the object.

A __type__ is a name used to denote a particular interface. An object can have many types.
A __type__ is a __subtype__ of another if its interface contains the interface of its __supertype__. Often you'll
  hear of a subtype _inheriting_ the interface of its supertype

# Diving into Common Patterns

## Adapter (Class, Object Structural)

__Adapter__ (aka __wrapper) lets classes work together that normally couldn't because of incompatible interfaces.


# Design Principles

Here's a list of design principles, which all you to ask questions about your proposed design.

* __Principle of least astonishment__ - don't be astonishing
* __Make common things easy and rare things possible__
* __Consistency__ - Do not pile on random rules
* __Law of Demeter__ - aka "don't talk to strangers"; an object should only reference itself, its
                       attributes, and the arguments of its methods. We want to "minimize coupling"
* __Independence__ or __Orthogonality__ - express independent ideas independently. This complements
    Separation, Encapsulation and Variation, and is part of the Low-Coupling-High-Cohesion message
* __Managed Coupling__ - Simply stating we have "low coupling" in a design is too vague - coupling
    happens and the important issue is to acknowledge it and control it
* __Subtraction__ - a design is finished when you cannot take anything else away
* __Simplicity before generality__ - "the simplest solution is the best"
* __Reflexivity__ (aka __Isomorphism__) - One abstraction per class, one class per abstraction
* __Once and once only__ - Avoid duplication of logic and structure where the duplication is not
    accidental (i.e. where both pieces of code express the same intent for the same reason)



