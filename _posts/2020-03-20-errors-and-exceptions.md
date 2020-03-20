---
layout: post
title: Errors and Exceptions
---


# {{ page.title }}

# Summary

With a lot of programming languages, the exception hierarchy is built around two categories:

* Errors - indicates serious problems that a reasonable application should not try to catch
* Exceptions - indicates conditions that a reasonable application might want to catch

One general way to think about it is if the exception happens during the runtime (exectuion) of the application.
A `SyntaxError`

Each of these have subclasses that depend on the language. For example, in Java it might look like:

## Errors Descendants

Error Descendants

* AssertionError - thrown when an assertion has failed
* LinkageError - thrown when a class dependency has some form of incompatibility

## Exception Descendants

Exception Descendants

* InterruptedException - thrown when a thread is active, but is somehow interrupted in the process
* RuntimeException - Thrown during normal execution of the application and operation of the Java Virtual Machine.
                     This category can include common exceptions like `ArithmeticException` and `IndexOutOfBoundsException`


## Ruby

In Ruby, exceptions are just classes in Ruby. The exception hierarchy is made up of all the classes that
inherit from the `Exception` class. Some examples include:

* NoMemoryError
* ScriptError
  * LoadError
  * NotImplementedError
  * SyntaxError
* SecurityError
* SignalException
  * Interrupt
* StandardError -- default for rescue
  * ArgumentError
    * UncaughtThrowError
  * EncodingError
  * FiberError
  * IOError
    * EOFError
  * IndexError
    * KeyError
    * StopIeration
  * LocalJumpError
  * NameError
    * NoMethodError
  * RangeError
    * FloatDomainError
  * RegexpError
  * RuntimeError -- default for raise
  * SystemCallError
    * Errno::
  * ThreadError
  * TypeError
  * ZeroDivisionError
* SystemExit
* SystemStackError

https://ruby-doc.org/core-2.5.0/Exception.html

### RuntimeError

`RuntimeError` is important because it is the default error class that is used when calling the
standard `Kernel#raise` method that we all use to handle exceptions (e.g. in your code, you might `raise`.

### Rescues

Exceptions are arranged into a class tree so you can easily rescue similar types of exceptions.


    begin
      do_something
    rescue StandardError => e
    end
