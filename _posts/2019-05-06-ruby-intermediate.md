---
layout: post
title: Intermediate Ruby 
---


# {{ page.title }}

Intermediate Ruby Notes

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
