---
layout: post
title: Intermediate Ruby 
---


# {{ page.title }}

Intermediate Ruby Notes

## Modules and Classes

A Class can be instantiated but a module cannot.
A module cannot be more than a library of methods.
A class can hold its state (by keeping track of instance variables) and can be duplicated as many times
as you want. If you ever need to instantiate something or have it exist over time, use a class instead of a module.
Think of a module as a library of methods that you can include in your classes.

### Modules

You can include a module of methods in your project by using `include` in your class. Now you have access
to all its methods as instance methods. This is the most common usage of modules, which add methods as instance
methods.

    class MyClass
      include MyModule
    end

You can also `extend` your module instead of including your module, although this is less common. When you
extend your module, it makes all the methods class methods.

    class MyClass
      extend MyModule
    end

Examples

    module ModuleExample
      def foo
        'foo'
      end
    end


    # Including modules binds the methods to the object instance
    class Person
      include ModuleExample
    end

    # IRB
    Person.foo # NoMethodError: undefined method 'foo' for Person:Class
    Person.new.foo # 'foo'


    # Extending modules binds the methods to the class itself
    class Book
      extend ModuleExample
    end

    # IRB
    Book.foo # 'foo'
    Book.new.foo # NoMethodError: undefined method 'foo'    

## Namespacing (module) for your Gem

You're using other people's Gems and marking the versions in a `Gemfile`.
But what happens if you need to make reusable code? The answer is that you create your own Gem.

Your gem should have a unique namespace (module) where all the code lives.
We do this so that you don't get namespace collisions with other gems.

## Loading code

Ruby has a few different ways of loading code. You can view `$LOADED_FEATURES` variable to see which files have
already been loaded into memory. Here's a few common ways to load files:

* `require` (as in `require 'bugsnag'` at the top of a file) will load directories relative to the `$LOAD_PATH`
* `require_relative` is similar to `require`, but looks within folders relative to the file it is being loaded from
* `require_all` - TODO: I haven't really seen used much
* `autoload`

## lib

Library code is in a `lib` directory. Looking at some example gems, we have:

    module Capybara
      # ...
      class CapybaraError < StandardError; end
      class DriverNotFoundError < CapybaraError; end
      class FrozenInTime < CapybaraError; end
      class ElementNotFound < CapybaraError; end
      # ...
    end
