---
layout: post
title: Ruby Rspec Tests
---


# {{ page.title }}

RSpec is a testing tool for Ruby to create behavior-driven development (BDD).

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

