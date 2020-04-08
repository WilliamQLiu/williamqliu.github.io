---
layout: post
title: Ruby Install
---


# {{ page.title }}

Ruby, just like any other language, has a lot of different ways to handle
different ruby versions and ruby libraries.

For a quick overview, we have the following:

* system ruby - the ruby that your OS uses
* RVM - is a tool for installing multiple versions of ruby on a single machine
        There's other ways to do this, including using say `rbenv` instead if you want
* bundler - based on your Gemfile, bundler runs `bundle install` to download and install all your gems.
            If you want to manually install each gem, you run `gem install <gem_name>`
            Bundler is packaged as a gem too
* gems - ruby libraries are installed as gems
         think of these as jars for Java
         Inside a gem, there's the ruby code, tests, and a __gemspec__ file (showing dependencies and version)
         A ruby project defines the gems it needs in a __Gemfile__
* gemset - a __gemset__ in __RVM__ is a set of gems specific to a given context, usually a project
           `rvm gemset list`
           `rvm gemset create mygemset` to create a new gemset
              or to be more specific `rvm --create --ruby-version ruby-1.9.3@mygemset`
           `rvm use 2.1.1@mygemset` or `rvm use @mygemset`

## Install Ruby

To install a system level ruby

For Ubuntu

    sudo apt-get install ruby`ruby -e 'puts RUBY_VERSION[/\d+\.\d+/]'`-dev
    sudo apt-get install ruby-dev

For Mac

    brew install ruby

## Ruby Version Manager (RVM)

### Install RVM

Install RVM here: https://rvm.io/

Install GPG keys:

    $gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB

Install RVM with default Ruby and Rails in one command

    $\curl -sSL https://get.rvm.io | bash -s stable --rails

Go through the rest of the install and you'll be able to:

### RVM Usage

See what ruby versions you have installed

    $ rvm list
    * ruby-2.3.4 [ x86_64 ]
       ruby-2.3.7 [ x86_64 ]
       ruby-2.4.1 [ x86_64 ]
       ruby-2.4.4 [ x86_64 ]
       ruby-2.5.0 [ x86_64 ]
       ruby-2.6.0 [ x86_64 ]
       ruby-2.6.2 [ x86_64 ]

See the current default version of ruby

    $ rvm --default use ruby-X.X.X

To install a specific version of ruby

    $ rvm install 2.6.0

To list out all possible installations of ruby, run:

    $ rvm list known

    # MRI Rubies
    [ruby-]1.8.6[-p420]
    [ruby-]1.8.7[-head] # security released on head
    [ruby-]1.9.1[-p431]
    [ruby-]1.9.2[-p330]
    [ruby-]1.9.3[-p551]
    [ruby-]2.0.0[-p648]
    [ruby-]2.1[.10]
    [ruby-]2.2[.10]
    [ruby-]2.3[.8]
    [ruby-]2.4[.5]
    [ruby-]2.5[.3]
    [ruby-]2.6[.0]
    ruby-head

    # for forks use: rvm install ruby-head-<name> --url https://github.com/github/ruby.git --branch 2.2

    # JRuby
    jruby-1.6[.8]
    jruby-1.7[.27]
    jruby-9.1[.17.0]
    jruby[-9.2.5.0]
    jruby-head

    # Rubinius
    rbx-1[.4.3]
    rbx-2.3[.0]
    rbx-2.4[.1]
    rbx-2[.5.8]
    rbx-3[.100]
    rbx-head

    # TruffleRuby
    truffleruby[-1.0.0-rc10]

    # Opal
    opal

    # Minimalistic ruby implementation - ISO 30170:2012
    mruby-1.0.0
    mruby-1.1.0
    mruby-1.2.0
    mruby-1.3.0
    mruby-1[.4.1]
    mruby-2[.0.0]
    mruby[-head]

    # Ruby Enterprise Edition
    ree-1.8.6
    ree[-1.8.7][-2012.02]

    # Topaz
    topaz

    # MagLev
    maglev-1.0.0
    maglev-1.1[RC1]
    maglev[-1.2Alpha4]
    maglev-head

    # Mac OS X Snow Leopard Or Newer
    macruby-0.10
    macruby-0.11
    macruby[-0.12]
    macruby-nightly
    macruby-head

    # IronRuby
    ironruby[-1.1.3]
    ironruby-head

## Bundle

Bundler is a tool for dependency management.
Bundler makes sure all your dependencies in your Gemfile are available to your application.
Bundle is a gem, even though it is used to install gems.
Bundle installs all the gems in your `Gemfile.lock`

    gem install bundler
    bundle update  # if needed update your bundler; your Gemfile.lock will show `Bundled With` and a version
    bundle install  # install the dependencies specified in your Gemfile, creates a `Gemfile.lock` if it does not exist

# RubyGems

## Gemsets

Before you install your gems, consider using a gemset. It's like a virtualenv with a specific name to handle
your group of gems for your program.

    rvm gemset create myproject

## Install Gems

Install your gems (i.e. libraries)

    sudo apt-get install rubygems

    gem env  # will show you where your gem paths are
    gem list  # lists out all your gems (like python's `pip freeze`)
    gem search [gem_name]
    gem install my_gem
    gem uninstall my_gem
    gem server  # to start up server to show what gems are installed

Example of gem list

    $ gem list

    *** LOCAL GEMS ***

    bundler (2.0.1, default: 1.17.2)
    bundler-unload (1.0.2)
    byebug (11.0.0)
    cmath (default: 1.0.0)
    coderay (1.1.2)
    concurrent-ruby (1.1.5)
    connection_pool (2.2.2)
    csv (default: 3.0.4)
    date (default: 2.0.0)
    sidekiq (5.2.5)
    simplecov (0.16.1)
    simplecov-html (0.10.2)

Linking to a local gem

    gem 'my-gem', '~> 3.5.0', :path => '/Users/william.liu/workspace/my-gem'


