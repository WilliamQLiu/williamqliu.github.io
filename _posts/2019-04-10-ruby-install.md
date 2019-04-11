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

    ast (2.4.0)
    aws-eventstream (1.0.2)
    aws-partitions (1.144.0)
    aws-sdk-core (3.46.2)
    aws-sdk-ec2 (1.72.0)
    aws-sdk-elasticache (1.10.0)
    aws-sdk-iam (1.13.0)
    aws-sdk-kms (1.13.0)
    aws-sdk-rds (1.44.0)
    aws-sdk-s3 (1.31.0)
    aws-sigv4 (1.0.3)
    bigdecimal (default: 1.4.1)
    bundler (2.0.1, default: 1.17.2)
    bundler-unload (1.0.2)
    byebug (11.0.0)
    cmath (default: 1.0.0)
    coderay (1.1.2)
    concurrent-ruby (1.1.5)
    connection_pool (2.2.2)
    csv (default: 3.0.4)
    date (default: 2.0.0)
    dbm (default: 1.0.0)
    did_you_mean (1.3.0)
    diff-lcs (1.3)
    docile (1.3.1)
    dotenv (2.7.1)
    e2mmap (default: 0.1.0)
    equatable (0.5.0)
    etc (default: 1.0.1)
    executable-hooks (1.6.0)
    faker (1.9.3)
    fcntl (default: 1.0.0)
    fiddle (default: 1.0.0)
    fileutils (default: 1.1.0)
    forwardable (default: 1.2.0)
    gdbm (default: 2.0.0)
    gem-wrappers (1.4.0)
    i18n (1.6.0)
    io-console (default: 0.4.7)
    ipaddr (default: 1.2.2)
    irb (default: 1.0.0)
    jaro_winkler (1.5.2)
    jmespath (1.4.0)
    json (2.2.0, default: 2.1.0)
    logger (default: 1.3.0)
    matrix (default: 0.1.0)
    method_source (0.9.2)
    minitest (5.11.3)
    mutex_m (default: 0.1.0)
    mysql2 (0.5.2)
    necromancer (0.4.0)
    net-telnet (0.2.0)
    openssl (default: 2.1.2)
    ostruct (default: 0.1.0)
    parallel (1.14.0)
    parser (2.6.0.0)
    pastel (0.7.2)
    pg (1.1.4)
    power_assert (1.1.3)
    prime (default: 0.1.0)
    pry (0.12.2)
    pry-byebug (3.7.0)
    psych (default: 3.1.0)
    rack (2.0.6)
    rack-protection (2.0.5)
    rainbow (3.0.0)
    rake (12.3.2)
    rdoc (default: 6.1.0)
    redis (4.1.0)
    redlock (1.0.0)
    rexml (default: 3.1.9)
    rspec (3.8.0)
    rspec-core (3.8.0)
    rspec-expectations (3.8.2)
    rspec-mocks (3.8.0)
    rspec-support (3.8.0)
    rss (default: 0.2.7)
    rubocop (0.66.0)
    ruby-progressbar (1.10.0)
    rubygems-bundler (1.4.5)
    rvm (1.11.3.9)
    scanf (default: 1.0.0)
    sdbm (default: 1.0.0)
    sequel (5.18.0)
    shell (default: 0.7)
    sidekiq (5.2.5)
    simplecov (0.16.1)
    simplecov-html (0.10.2)
    stringio (default: 0.0.2)
    strings (0.1.4)
    strings-ansi (0.1.0)
    strscan (default: 1.0.0)
    sync (default: 0.5.0)
    test-unit (3.2.9)
    thor (0.20.3)
    thwait (default: 0.1.0)
    tiny_tds (2.1.2)
    tracer (default: 0.1.0)
    tty-color (0.4.3)
    tty-cursor (0.6.1)
    tty-screen (0.6.5)
    tty-table (0.10.0)
    unicode-display_width (1.4.1)
    unicode_utils (1.4.0)
    webrick (default: 1.4.2)
    xmlrpc (0.3.0)
    zlib (default: 1.0.0)

