---
layout: post
title: Clean Architecture
---


# {{ page.title }}

Notes from "Clean Architecture: A Craftsman's Guide to Software Structure and Design"

## Summary

When software is done right, it requires a fraction of the human resources to create and maintain.
Our goal is to minimize the human resources required to build and maintain our required systems.

# Section I

## 1 - What is Design and Architecture

### Good vs Bad Design

Good design quality means that you have low effort and this effort stays low throughout the life of the system.
Bad design is if the effort grows with each new release.

## 2 - Two Values (Behavior and Architecture)

Every software system provides two different values to the stakeholders: __behavior__ and __structure__

* Behavior is where programmers make machines behave in a way that makes or saves money for staekholders
* Structure is where 'software' has to remain 'soft' (must be easy to change the behavior)

### Eisenhower Urgent-Important Matrix

Eisenhower said "I have two kinds of problems, the urgent and the important. The urgent are not important,
and the important are never urgent". Those things that are urgent are rarely of great importance, and those
things that are important are seldom of great urgency. We can arrgant into quadrants with actions:

* Important Urgent - Do
* Important Not Urgent - Plan
* Not Important Urgent - Delegate
* Not Important Not Urgent - Eliminate

'Behavior' is always urgent, but not always particularly important (Important Urgent, Not Important and Urgent)
'Architecture' is always important, but not always particularly urgent (Important Urgent, Important and Not Urgent)

Is is the responsibility of the software development team to assert the importance of architecture over the
urgency of features. Making messes is always slower than staying clean.

# Section II - Start with the Bricks: Programming Paradigms

There have been a few 'revolutions' in programming already.

* 1938 Turing is writing real programs using loops, branches, assignment, subroutines, stacks and other structures in binary
* 1951 Grace Hopper invents __A0__, the first compiler and invents Fortran in 1953, followed by many new programming languages
* Then __programming paradigms__ are created (ways of programming mostly unrelated to languages so you know what programming structure to use).

## 3 - Paradigm Overview

There are three programming paradigms:

* __Structured Programming__
* __Object-Oriented Programming__
* __Functional Programming__

Each paradigm imposes discipline on something; it __removes__ some type of capability from the programmer.
Each imposes some kind of extra discipline that is negative in its intent; the paradigm tells us what NOT to do
more than they tell us what to do.

### Structured Programming

__Structured Progrmaming__ imposes discipline on direct transfer of control.
Basically, `goto` statements were harmful and should be replaced with `if/then/else` and `do/while/until`.

Notes:

* All programs can be constructed from just three structures: sequence, selection, and iteration
* All that tests can do, after sufficient testing effort, is allow us to deem a program to be correct enough for our purposes
* Structured Programming forces us to recursively decompose a program into a set of small provable functions

### Object-Oriented Programming

__Object-Oriented Programming__ imposes discipline on indirect transfer of control.
Basically, __polymorphism__ was discovered through the disciplined use of function pointers.

Notes:

* the basis of good architecture is the understanding and application of the principles of object-oriented design (OO)

### What is Object-Oriented Design (OO)?

Answers:

* The combination of data and function; this is really vague
* A way to model the real world. But what does that mean?
* Encapsulation, Inheritance, and Polymorphism.

#### Encapsulation

__Encapsulation__ hides data by wrapping data and the methods that work on data within one unit in order to prevent
the accidental modification of an object's components. Publically accessible methods are provided in the class with
`getters` and `setters`.

* OO languages do provide easy and effective encapsulation of data and function.
* A line can be drawn around a cohesive set of data and functions. Outside of this line, data is hidden and only some functions are known.
* For example, there might be private data members and the public member functions of a class. A programming language
  like Python (an OO language) has no real enforced encapsulation (everything is public and encapsulation is done by convention using `_` in naming).

#### Inheritance

__Inheritance__ is the redeclaration of a group of variables and functions within an enclosing scope.
While modern programming languages make this a lot easier, it was possible with non OO languages (just more difficult to do/tricky)

#### Polymorphism

__Polymorphism__ means that objects of different types can be accessed through the same interface. Each type can
provide its own, independent implementation of this interface. At its core, polymorphism is an application of pointers
to functions (we just don't explicitly use pointers to functions to create polymorphic behavior is because it is dangerous).
OO languages makes polymorphism trivial.

Instead of requiring an inheritance relationship, we can now point to the opposite direction (__dependency inversion__).
This means that any source code dependency, no matter where it is, can be inverted. Any source code dependency can
be turned around by inserting an interface between them. Software architects have absolute control over the direction
of all source code dependences in thes system (we do not have to align these dependencies with the flow of control).

With Polymorphism, we can have components deployed separately and independently (__independent deployability__)

### Functional Programming

__Functional Programming__ imposes discipline upon assignment.
Basically, the values of symbols do not change; there is no assignment statement.

Notes:

* Variables in functional languages do not vary
* All race conditions, deadlock conditions, and concurrent update problems are due to mutable variables
* All the problems we face in concurrent applications (e.g. requiring multiple threads, processors) cannot happen if there is no mutable variables
* What can we do? Segregate the application/services into mutable and immutable components (with immutable
  components performing tasks in a functional way, without using mutable variables)
* Protect mutable variables from concurrent updates and race conditions using some kind of __transactional memory__ (protects
  these variables with a transaction-or-retry-based scheme)
* __Event Sourcing__ is a strategy where we store the transactions, but not the state; if state is needed, apply all transactions from the start


## 3 - Design Principles

The architecture doesn't matter if the bricks are bad. We use the __SOLID__ principles to tell us how to setup our
bricks (i.e. how to arrange our functions and data structures into classes, and how those classes should be interconnected).
This is for __mid-level software structures__ (think modules, class design).

So what is a class? A __class__ is simply a coupled grouping of functions and data.
So what is our goal? With SOLID, we want to be able to create __mid-level software structures__ (i.e. for modules) that are:

* tolerant of change
* are easy to understand
* are the basis of components that can be used in many software systems

### SOLID Principles

The SOLID principles are:

* SRP - The Single Responsibility Principle - each software module has one, and only one, reason to change
* OCP - The Open-Closed Principle - for software systems to be easy to change, they must be designed to allow the
  behavior of those systems to be changed by adding new code, rather than changing existing code
* LSP - The Liskov Substitution Principle - to build software systems from interchangeable parts, those parts must
  adhere to a contract that allows those parts to be substituted one for another
* ISP - The Interface Segregation Principle - avoid depending on things that they don't use
* DIP - The Dependency Inversion Principle - the code that implements high-level policy should not depend on the code
  that implements low-level details. Details should depend on Policies.

#### SRP: The Single Responsibility Principle

__The Single Responsibility Principle__ means that a __module__ should have one, and only one, reason to change.
This is NOT the same as a __function__ should do one, and only one thing. Instead, a better way of thinking about
this principle is that __a module should be responsible to one, and only one, actor__.

So what is a `module`? This can be a source file or a cohesive set of functions and data structures.

What happens if you violate this principle?

* Accidental Duplication - (e.g. CFO, COO, CTO using different methods in an Employee class so that there is tight
  coupling between the COO's methods from affecting say the CTO). __Separate the code that different actors depend on.__
* Merges - (e.g. CTO wants to change the Employee table, but so does COO); again, separate code that different actors depend on

##### Facade Pattern

You can separate the data from the functions. You can have three classes that are not allowed to know about each other,
but they share access to say an `EmployeeData` class, which is a simple data structure with no methods.

With the __Facade Pattern__, we can have an `EmployeeFacade` that has little code, but is responsible for instantiating
and delegating to the classes with the functions. Each of the classes that has a family of methods is a __scope__.
Outside of that scope, no one knows that the private members of the family exist.

In my experience, this can get a little out of hand (where there are way too many classes).

#### OCP: The Open-Closed Principle

__The Open-Closed Principle__ means that a __software artifact should be open for extensions but closed for modification__.
You should be able to extend the software's behavior without having to modify that artifact.

A good software architecture can reduce the amount of changed code to the barest minimum (ideally zero). The goal is
to make the system easy to extend without incurring a high impact of change. We can accomplish this goal by
partitioning the system into components, and arranging these components into a dependency hierarchy that protects
high-level components form changes in lower-level components.

Summary: __You should be able to extend a classes behavior, without modifying it.__

#### LSP: The Liskov Substitution Principle

__The Liskov Substitution Principle__ says that subtypes are defined as the following substitution property:

If for each object 01 of type S there is an object 02 of type T such that for all programs P defined in terms of T,
the behavior of P is unchanged when 01 is substituted for 02 then S is a subtype of T.

Summary: __Derived classes must be substitutable for their base classes.__

##### The Square/Rectangle Problem

Say you have a Rectangle and Square. Square is not a subtype of Rectangle because a Rectangle has independently mutable
height and width. A Square by definition has the same height and width that changes together.

What happens if you violate this principle? You might be tempted to put in if-statements to substitute say a REST
endpoint (e.g. if endpoint ends with `dest` from `acme.com` instead of the regular `destination`). However, the
better scenario would be to add a configuration database keyed by the dispatch URI (e.g. acme.com -> use `dest` endpoint)

Substitution works great, until a simple violation of substitutability, which can cause a system's architecture
to be polluted with a significant amount of extra mechanisms.

#### ISP - The Interface Segregation Principle

__The Interface Segregation Principle__

It is harmful to depend on modules that contain more than you need. Depending on something that carries baggage
that you don't need can cause you troubles that you don't expect.

Summary: __Make fine grained interfaces that are client specific.__

#### DIP - The Dependency Inversion Principle

__The Dependency Inversion Principle (DIP)__ tells us that the most flexible systems are those in which source code
dependencies refer only to abstractions, not to concretions. When you import a library, the source code dependencies
should not refer to concrete modules (e.g. a module in which the functions being called are implemented).

In reality, you cannot abstract away say the `String` class. Changes to that class are rare and tightly controlled.
We can rely on the operating system and standard libraries; it is the __volatile__ concrete elements of our system
that we want to avoid depending on.

So why the interface? Interfaces do not change as often as the concrete implementation. We should follow:

* Don't refer to volatile concrete classes. Refer to abstract interfaces instead. We enforce the use of Abstract Factories.
* Don't derive from volatile concrete classes. Remember that inheritance is the strongest, most rigid of all the source
  code relationships; it should be used with great care.
* Don't override concrete functions. Concrete functions often require source code dependencies. When you override
  these functions, you do not eliminate the dependencies (instead you inherit them). Make the function abstract and
  create multiple implementations.
* Never mention the name of anything concrete and volatile.

Summary: __Depend on abstractions, not on concretions.__

##### Factories

Use an __Abstract Factory__ to manage the undesirable dependencies listed above.


