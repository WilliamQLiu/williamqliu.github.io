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

There are three paradigms:

* __Structured Programming__
* __Object-Oriented Programming__
* __Functional Programming__




