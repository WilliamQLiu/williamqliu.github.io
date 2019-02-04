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

## Create a new Controller and View

We need to create a new controller by running the 'controller' generator and telling it
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
This tells Rails to map requests to the root of the application to the welcome controller's index action and 
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

## Debugging

When you're in your app directory, you can get into a console to debug issues:

    $rails console

    Post.first
    Post.first.comments
    Post.first.comments.create! body: 'Say something funny!'
