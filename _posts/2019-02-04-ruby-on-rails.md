---
layout: post
title: Ruby on Rails
---


# {{ page.title }}

__Ruby on Rails__ is a modern web application framework.

# Install

Ruby
SQLite3

## Create a new Ruby on Rails app

    $ rails new blog

## Start the Web Server

Start the webserver to run at http://localhost:3000 by default

    $ bin/rails server

## We have the following directories setup

* `app` - has the controllers, models, views, helpers, mailers, channels, jobs and assets
* `bin` - contains the rails script that starts your app and has other scripts (used to setup, update, deploy, run your app)
* `config` - your application's routes, databases, etc.
* `db` - contains your current database schema and database migrations
* `Gemfile`, `Gemfile.lock` - specify what your gem dependencies are; files are used by __Bundler__
* `lib` - extended modules for your application
* `log` - application log files
* `package.json` - specify what npm dependencies are needed for your Rails app; file is used by __Yarn__
* `public` - static files and compiled assets
* `Rakefile` - file that locates and loads tasks that can be run from the command line; tasks are defined throughout
               components of Rails. Do not modify the Rakefile, instead add tasks through the `lib/tasks` dir
* `test` - unit tests, fixtures
* `tmp` - temporary files (cache, pid files)
* `vendor` - third-party code (e.g. vendored gems)
* `.ruby-version` - the default Ruby version

## Generate vs Scaffold

To generate models or resources, you can either generate individual models, resources or do this with scaffolding to setup a lot for you

  rails generate model MyModelName myfieldname:text  # Will generate a model in your 'models' dir and a migration file in 'db/migrate/ dir
  rails generate controller MyControllerName MyActionName
  rails generate resource MyResourceRoute name:my_table # Will generate the above model, migration file, as well as controller in `controllers` dir and 'resource' route in 'routes.rb' file
  rails generate scaffold Micropost context: text user_id: integer  # generates the above, but in controller file, also adds a lot more methods (e.g. GET, POST, PATCH, DELETE)

Shortcut: `rails g` is the shortcut for `rails generate`
Note: `rails destroy` (aka `rails d`) does the opposite of `generate` and will undo.

### Create a new Controller and View

To manually create items, we need to create a new controller by running the 'controller' generator and telling it
we want a controller named 'Welcome' with an action called 'index'

    $ bin/rails generate controller Welcome index

      create  app/controllers/welcome_controller.rb
       route  get 'welcome/index'
      invoke  erb
      create    app/views/welcome
      create    app/views/welcome/index.html.erb
      invoke  test_unit
      create    test/controllers/welcome_controller_test.rb
      invoke  helper
      create    app/helpers/welcome_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/welcome.coffee
      invoke    scss
      create      app/assets/stylesheets/welcome.scss

We generated:

* A controller at `app/controllers/welcome_controller.rb`
* A view at `app/views/welcome/index.html.erb`

### Setup routing

Open the `config/routes.rb` file and add `root 'welcome#index'`
This tells Rails to map requests to the root of the application to the welcome controller's `index` action and
get `welcome/index` so that requests go to `http://localhost:3000/welcome/index`

i.e. GET `/welcome/index/` returns to `welcome#index`, which is our template in `/app/view/welcome/welcome.index.erb`

### Resources and Routes

Rails provides a __resources__ method which can be used to declare a standard REST resource.
You need to add the 'article' resource to the `config/routes.rb` file.

Under `bin/rails routes` or `bin/rake routes` (depending on version), you should see every REST endpoint now

    $bin/rake routes
           Prefix Verb   URI Pattern                  Controller#Action
    welcome_index GET    /welcome/index(.:format)     welcome#index
         articles GET    /articles(.:format)          articles#index
                  POST   /articles(.:format)          articles#create
      new_article GET    /articles/new(.:format)      articles#new
     edit_article GET    /articles/:id/edit(.:format) articles#edit
          article GET    /articles/:id(.:format)      articles#show
                  PATCH  /articles/:id(.:format)      articles#update
                  PUT    /articles/:id(.:format)      articles#update
                  DELETE /articles/:id(.:format)      articles#destroy
             root GET    / ### Generate Controller for Articles

### Generate Controller for Articles

When we visit `http://localhost:3000/articles`, we get an error with `uninitialized constant ArticlesController`

You can generate with:

    $bin/rails generate controller articles
      create  app/controllers/articles_controller.rb
      invoke  erb
      create    app/views/articles
      invoke  test_unit
      create    test/controllers/articles_controller_test.rb
      invoke  helper
      create    app/helpers/articles_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/articles.coffee
      invoke    scss
      create      app/assets/stylesheets/articles.scss

We then created the `app/controllers/articles`

### Implement methods for Articles Controller

In `app/controllers/articles_controller.rb`, we need to define a method for `new`

    class ArticlesController < ApplicationController
      def new
      end
    end

## Debugging with Rails Console

When you're in your app directory, you can get into a console to debug issues:

    $rails console

    Post.first
    Post.first.comments
    Post.first.comments.create! body: 'Say something funny!'

### Rails Console Sandbox

If you want to run without changing any data, use sandbox: `rails console --sandbox`

### Rails Console DB

If you want to run the cli for your database, run: `rails dbconsole`

## Rake

__Rake__ is `Ruby Make`, a standalone Ruby utility that replaces UNIX `make`. We have a `Rakefile` and a `.rake` files
to build up a list of tasks. In Rails, Rake is used for common administration tasks, especialy sophisticated ones that
build off of each other.

### Rake Tasks

To see a list of Rake tasks available to you, run `rake --tasks`. Each task has a description.

    rake --tasks

## Models

### Validation

You want to make sure that only valid data is saved to your database. Check if valid with `.valid?`

    Person.create(name: "John Doe").valid? # => true
    Person.create(name: nil).valid? # => false

These methods trigger validations:

* create
* create!
* save
* save!
* update
* update!

The __bang__ versions (e.g. `save!`) raise an exception if the record is invalid. The non-bang versions don't
so be careful using those.

Some methods do not trigger validations, including:

* decrement!
* decrement_counter
* increment!
* increment_counter
* toggle!
* touch
* update_all
* update_attribute
* update_column
* update_columns
* update_counters

### Model Associations

You can create associations between data models. Say we have a `User` Model, each potentially connected to
many `Micropost` models. The key attributes here are `has_many` and `belongs_to`.

    #app/models/user.rb
    class User < ApplicationRecord
      has_many :microposts
    end

    #app/models/micropost.rb
    class Micropost < ApplicationRecord
      belongs_to :user
      validates :content, length: { maximum: 140 }
    end

### Model Inheritance

Models inhert from other models using `<`. Here's an example:

    #app/models/micropost.rb
    class Micropost < ApplicationRecord
    end

The `Micropost` Model inherits from `ApplicationRecord`, which then inherits from the base class `ActiveRecord::Base` Model

## Controllers

### Controller Inheritance

Controllers also inherit from other controllers using `<`, similar to Models. Here's an example:

    #app/controllers/microposts_controller.rb
    class MicropostsController < ApplicationController
    end

    #app/controllers/application_controller.rb
    class ApplicationController < ActionController::Base
    end

The `MicropostsController` Controller inherits from `ApplicationController`, which then inherits from the base class `ActionController::Base`
Some of the functionality that is inherited from the base class `ActionController::Base` includes the ability to
manipulate model objects, filter inbound HTTP requests, and render views as HTML.

Since all Rails controllers inherit from `ApplicationController`, rules defined in the Application controller are
automatically applied to every action in the application.

## Tests

To run tests, do `rails test`

## ActiveRecord

__ActiveRecord__ is the __M__ (model) in __MVC__. ActiveRecord helps with the creation and use of objects
whose data requires persistent storage to a database. ActiveRecord is an implementation of the __Active Record pattern__,
which is a description of an __Object Relational Mapping__ system.

### Active Record Pattern

In the book 'Patterns of Enterprise Application Architecture', Active Record pattern says that objects carry both
persistent data and behavior which operates on that data. Active Record takes the opinion that ensuring data access
logic as part of the object will educate users of that object on how to read and write to a database.

### Transactions

* A transaction acts on a single database connection (not distributed across db connections)
* `save` and `destroy` are wrapped in a transaction

Transaction Code

For this example, we take money from will and pass to laura only if neither `withdrawal` or `deposit` raises an exception.
Exceptions will force a __rollback__ that returns the database to the state before the transaction began.
However, objects will NOT have their instance data returned to their pre-transactional state.

    ActiveRecord::Base.transaction do
      will.withdrawal(100)
      laura.deposit(100)
    end

Exception Handling

There's a couple ways to make sure that your transaction worked OR is rolled back correctly.

1. You can use validations to check for values that the transaction depends on
2. You can raise exceptions in the callbacks to the rollback, including `after_*` callbacks

Exceptions thrown within a transaction block will be propagated (after triggering the ROLLBACK), so be ready to
catch those in your application code. An example of an exception is the `ActiveRecord::Rollback` exception, which
will trigger a ROLLBACK when raised, but not be re-raised by the transaction block.
