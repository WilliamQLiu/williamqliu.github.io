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

## `<<`

Remember that everything in Ruby is an object. You are able to add methods to a specific instance.
When you use the following `<<` syntax, you open up your object's singleton class (eigenclass).
This allows you to specialise the behavior of methods called on that specific object.

    a = 'foo'

With `class << self`, we open up the `self`'s singleton class so the methods can be redefined for the current
`self` object (which inside a class or module body is the class or module itself). Usually this is used to
define class/module ('static') methods.

    class String
      class << self
        def value_of obj
          obj.to_s
        end
      end
    end

    # which can be written as
    class String
      def self.value_of obj
        obj.to_s
      end
    end

    # which can be written as
    def String.value_of obj
      obj.to_s
    end

If you dont't want to type `self.` in front of each definition, you can do `class << self`. Otherwise, it
might be clearer that the method is static if you explicitly say `def self.some_method`.

## Class Variables with `@@`

`@@` before a variable name means it is a class variable, meaning one per class (i.e. this variable is shared
across all classes). If you modify the variable in an instance of one, you modify the variable for all other
instances and even child instances. You can access these variables in both instance and class methods of the class.

## Instance Variables with `@`

In Ruby, we save an instance variable using the `@` syntax. All instance variables are private. In order
to access instance variables, you need to setup getters and setters. We need an interface, like:

    class Pet

      def play_chess
        puts "Now playing chess"
      end

      def super_powers(powers)
        @super_powers = powers

      def super_powers
        @super_powers
      end
    end

    flopsy = Pet.new # create a new instance of the Pet class
    flopsy.class  # => Pet
    flopsy.super_powers = "Flight"  # set
    floopsy.super_powers # => "Flight"  # get

These instance variables are an object's attributes.

## attr method

Since class variables are so common, ruby has shortcuts for creating class variables and their associated
getters and setters. The `attr` method creates an attribute and its associated getter. If the second parameter
is true, a setter is created too. The `attr_reader` and `attr_writer` methods create getters and setters independently.
We can also use the `attr_accessor` method to define the getters and setters.

    class Fruit
      def set_kind(k)  # a writer
        @kind = k
      end
      def get_kind  # a reader
        @kind
      end
    end

    f1 = Fruit.new  # <Fruit:0x93fwec>
    f1.set_kind("peach")    # use the writer
    f1.get_kind     # use the reader
    f1  <Fruit:0x93fwec @kind="peach">

Shortcuts:

    Shortcut                Effect
    attr_reader :v          def v; @v; end
    attr_writer :v          def v=(value); @v=value; end
    attr_accessor :v        attr_reader :v; attr_writer :v
    attr_accessor :v, :w    attr_accessor :v, attr_accessor :w

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
