---
layout: post
title: Jetbrains IDE
---


# {{ page.title }}

I'm using JetBrains IntelliJ IDEA Ultimate. Some of the tools include:

* DataGrip - connect to databases
* RubyMine - for Ruby

## Configuration

## Project Structure

Setup your Project SDK (e.g. ruby-2.6.2)
Setup your Modules (e.g. click 'Sources' to identify code, 'Tests' for tests)

### Add a Content Root

In IntelliJ, the top level folder that has all of your files that contain your source code, build scripts, tests,
is called a __content root__. There is normally one content root. You should see this appear next to 'Add Content Root'.

https://www.jetbrains.com/help/idea/content-roots.html

### Folder Categories

There are a few ways you can categorize your folders. These include:

* __Sources Root__ - production code that should be compiled
* __Generated Sources Root__ - generated automatically rather than written manually, can be regenerated
* __Test Sources Root__ - keep tests separate from production code
* __Resources Root__ - (Java Only) - Resource files used in your application (e.g. images, configuration XML, and properties files)
* __Test Resources Root__ - these folders are for resource files associated with your test sources
* __Excluded__ - files in these folders are ignored by code completion, navigation, and inspection
* __Load Path Root__ - (Ruby only) - the load path is where the `require` and `load` statements look for files

https://www.jetbrains.com/help/idea/content-roots.html#folder-categories

#### Example Ruby Folder Categories

For a gem:

  .buildkite - Excluded
  .bundle - Excluded
  .idea - Excluded
  bin - Resources
  coverage - Excluded
  docs - Resources
  exec - Excluded
  lib - Sources
  out - Excluded
  spec - Tests
