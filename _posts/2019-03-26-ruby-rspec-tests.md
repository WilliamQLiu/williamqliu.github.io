---
layout: post
title: Ruby Rspec Tests
---


# {{ page.title }}

RSpec is a testing tool for Ruby to create behavior-driven development (BDD).
RSpec is made up of multiple libraries that are designed to work together or can
be used independently with other testing tools like __Cucumber__ or __Minitest__.
The parts are:

* __rspec-core__ - the spec runner, which provides a command line program, flexible
  and customizable reporting, and an API to organize your code examples.
* __rspec-expectations__ - provides a readable API to express expected outcomes of
  a code example
* __rspec-mocks__ - test double framework, providing multiple types of fake objects
  to allow you to tightly control the environment in which your specs run
* __rspec-rails__ - supports using rspec to test Ruby on Rails applications in place
  of Rails' built-in test framework

## Relish

RSpec is documented through executable examples on Relish: http:relishapp.com/rspec

The examples are written in an "end-to-end" style demonstrating the use of various
RSpec features in the context of executable spec files. If you want detailed
documentation about a particular API or feature, use the API docs instead.

## Cucumber

__Cucumber__ provides executable documentation and provides reports indicating
whether the software behaves according to the specification or not.

## Test Driven Development (TDD)

BDD's parent is test-driven development (TDD), which means we work in a red-green
loop for writing small tests so that the test initially fails, then passes after
writing our code.

## Behavior Driven Development (BDD)

BDD is a concept build on top of TDD, where we write tests as specifications of
system behavior. With RSpec, we are describing the behavior of classes, modules,
and their methods.

* Tests are called `specs` (specifications)
* Tests are stored in a spec directory

Run specs with `bundle exec rspec`

## Code coverage with Simplecov

https://github.com/colszowka/simplecov

Install the `simplecov` gem, then add SimpleCov to the top of your `spec_helper.rb` file.

    require 'simplecov'
    SimpleCov.start

You should then get a directory called `coverage` with your results

One thing to note is that you want to put SimpleCov above everything else. For example, if you have some code
`require 'mycode'` that you want covered, make sure that `require 'simplecov'` and `SimpleCov.start` is above your
other require.


