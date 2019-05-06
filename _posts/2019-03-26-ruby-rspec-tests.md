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

## Describing Methods

So RSpec is a behavior-driven development process of writing human readable specifications.
The basic structure is that Rspec uses the words `describe` and `it` so we can express concepts like a conversation:

### `describe` and `it`

* "Describe an order."
* "It sums the prices of its line items."

### `describe` and `context`

`describe` and `context` group related tests together
They are the same method.

So when do you use `describe` and when do you use `context`?

* Use `describe` for __things__
* Use `context` for __states__

Bad:    `context "#matriculate"
Better: `describe "#matriculate"

Bad:    `describe "when the student is sick"`
Better: `context "when the student is sick"`

### `context`

* Prefer `context` since tests usually deal with permutations of state
* `context` names should not match your method names
* Explain __why__ a user would call the method

Example:

Bad:    `describe "#assign"`
Better: `context "assigning homework to a student"`

### Nested Groups with `describe` and/or `context`

RSpec has two scopes:

* __Example Group__ are groups defined by a `describe` or `context` block
* __Example__ are typically defined by an `it` block, which are evaluated in the context of an _instance_ of the
              example group class to which the example belongs.

Example of a nested group:

    RSpec.describe Order do
      context "with no items" do
        it "behaves one way" do
          # ...
        end
      end

      context "with one item" do
        it "behaves another way" do
          # ...
        end
      end
    end

## `before` vs `let`

`before` eagerly runs code before each test
`let` lazily runs code when it is first used

Use `before` for _actions_
Use `let` for _dependencies_ (real or test double)

Examples:

Bad:    let(:dummy) do
          @classroom.initialize_roster
        end
Better: before do
          @classroom.initialize_roster
        end

Bad:    before { @grade_levels = [1, 2, 3] }
Better: let(:grade_levels) { [1, 2, 3] }

## `before` with `context`

* `context` is used for state
* `before` lists the actions to get to that state

## Remove weak words

* Don't use "should" in your test names
* Say what will happen
* Running the test will tell you if it is true or not
* Prefer active verbs as in "France __beat__ Brazil in the finals."
* Do not use passive verbs as in "Brazil __was beaten__ by France in the finals"

Bad:    it "should add the student to the class"
Better: it "adds the student to the class"

## Sources

http://www.betterspecs.org/
https://github.com/rubocop-hq/rspec-style-guide
http://jakegoulding.com/presentations/rspec-structure/#slide-1
