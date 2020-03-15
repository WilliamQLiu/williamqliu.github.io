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


