---
layout: post
title: Backbone.js 
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
*  [Dependencies](#dependencies)
*  [API Integration](#apiintegration)
*  [Models]
*  [Collections]
*  [Views]
*  [Routing with URLs]
*  [Events]

##<a id="summary">Summary</a>

Backbone.js is a JavaScript MV* for front-end web development.  It's basically a huge step up from pulling and pushing data (e.g. pull with AJAX GET, push with WebSockets using a persistent connection between client and server) to manipulate data (where you previously would have to write the glue code that looks at the DOM, finds an element with a specific id, and update the HTML manually).  Backbone is pre-configured to sync with a RESTful API and is great for building single page applications (don't have to wait for entire page to reload for everytime we change the underlying data).

Note: For server stuff, Django is mainly a synchronous framework (a request goes in, server computes stuff, then a response goes out).  If you need a persistent connection, change your back-end server to something like Tornado or Twisted (which complicates projects because of connections breaking).

The idea behind Backbone is that you represent your data with __Models__, which can be created, validated, destroyed, and saved to the server.  When a UI action causes an attribute to change, the model triggers a 'change' event.  The change event notifies the __Views__ that display the model's state and are able to respond accordingly by re-rendering themselves with the new information.

Backbone is a minimal way of separating business logic from user interface:

*  Data Structure
    -  Models (orchestrates data and business logic, loads and saves from server, emits events when data changes)
    -  Collections (a group of related models)
*  User Interface
    -  Views (listens for changes from events and renders UI, handles user input and interactivity, sends captured input to the model)
    -  Routing with URLs

##<a id="dependencies">Dependencies</a>

Backbone has a fixed dependency on __Underscore.js__ to help with filtering and sorting data.  Backbone uses __jQuery__ for manipulating the Document Object Model (DOM).

##<a id="apiintegration">API Integration</a>

Backbone is pre-configured to sync with a RESTful API.

##<a id="nonjstemplate">Non-Javascript part of Template</a>

The non-js part of our template.

    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8">
      <title>title-backbonejs</title>
    </head>
    <body>
      <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>
      <script src="http://ajax.cdnjs.com/ajax/libs/underscore.js/1.8.3/underscore-min.js"></script>
      <script src="http://cdnjs.cloudflare.com/ajax/libs/backbone.js/1.2.1/backbone-min.js"></script>
    
      <script src="...number.js..." type="text/javascript"></script>
    </body>
    </html>

##<a id="views">Views</a>

The View is the user interface and tells us how data is displayed.  Backbone views are generally based on View Templates.  While rendering the UI, Views also listen to the events from the DOM.

####Declaration and Instantiation of a minimalist View with `initialize` and `render`

Upon instantiation, we automatically call `initialize()`.  This handles all bindings except for UI events like clicks.  We then `render()` the view, in this case inside an existing element.

    (function($){  // creates a self-executing wrapper
      var ListView = Backbone.View.extend({  // our main app's view
        el: $('body'),  // attaches 'this.el' to an existing element
        
        initialize: function(){  // automatically call upon instantiation; do all bindings except for UI events (e.g. clicks)
          _.bindAll(this, 'render'); // fixes loss of context for 'this' within methods
    
          this.render();  // not all views are self-rendering; this one is
        },
    
        render: function(){  // function to render the entire view in 'this.el'
          $(this.el).append("<ul> <li>hello world</li> </ul>");
        }
      });
      
      var listView = new ListView();  // instantiate main app view
    })(jQuery);

####Binding of DOM events to View methods with `events`

Views act like Controllers in a MVC and are used to bind DOM events to View methods.  Under 'events', you can see that we have a 'click' that triggers the custom 'addItem' function.

    (function($){  // creates a self-executing wrapper
      var ListView = Backbone.View.extend({  // our main app's view
        el: $('body'),  // attaches 'this.el' to an existing element
    
        events: {
            'click button#add': 'addItem'
        },
        
        initialize: function(){  // automatically call upon instantiation; do all bindings except for UI events (e.g. clicks)
          _.bindAll(this, 'render', 'addItem'); // every function that uses 'this' as the current object should be here
    
          this.counter = 0;  // total number of items added so far
          this.render();  // not all views are self-rendering; this one is
        },
    
        render: function(){
          $(this.el).append("<button id='add'>Add list item</button>");
          $(this.el).append("<ul></ul>");
        },
    
        addItem: function(){
            this.counter++;
            $('ul', this.el).append("<li>hello world"+this.counter+"</li>");
        }    
      });
      
      var listView = new ListView();  // instantiate main app view
    })(jQuery);

##<a id="models">Models and Collections</a>

A Model is a JavaScript object (i.e. key-value pairs) with some helper functions to handle things like event triggering, persistence.  A Collection is a group of Models.

####How to use a Collection of Models to store data and tie changes to a View

    (function($){  // creates a self-executing wrapper
    
      var Item = Backbone.Model.extend({
        defaults: {
            part1: 'hello',
            part2: 'world'
        }
      });
      
      var List = Backbone.Collection.extend({
        model: Item
      });
    
      var ListView = Backbone.View.extend({  // our main app's view
        el: $('body'),  // attaches 'this.el' to an existing element
    
        events: {
            'click button#add': 'addItem'
        },
        
        initialize: function(){  // automatically call upon instantiation; do all bindings except for UI events (e.g. clicks)
          _.bindAll(this, 'render', 'addItem', 'appendItem'); // every function that uses 'this' as the current object should be here
    
          this.collection = new List();
          this.collection.bind('add', this.appendItem);  // collection event binder
    
          this.counter = 0;  // total number of items added so far
          this.render();  // not all views are self-rendering; this one is
        },
    
        render: function(){
          var self = this;  // save reference to 'this' so it can be accessed from within the scope of the callback
          $(this.el).append("<button id='add'>Add list item</button>");
          $(this.el).append("<ul></ul>");
          _(this.collection.models).each(function(item){
            // in case collection is not empty
            self.appendItem(item);
          }, this);
        },
    
        // addItem now works with just the models/collections
        addItem: function(){
          this.counter++;
          var item = new Item();
          item.set({
            part2: item.get('part2') + this.counter  // modify item defaults
          });
          this.collection.add(item);  // add item to collection; view is updated via event 'add'
        },
        
        appendItem: function(item){
          $('ul', this.el).append("<li>"+item.get('part1')+" "+item.get('part2')+"</li>");
        }
    
      });
      
      var listView = new ListView();  // instantiate main app view
    })(jQuery);

####Delegate rendering of a Model to a dedicated View

    (function($){  // creates a self-executing wrapper
    
      var Item = Backbone.Model.extend({
        defaults: {
            part1: 'hello',
            part2: 'world'
        }
      });
      
      var List = Backbone.Collection.extend({
        model: Item
      });
    
      var ItemView = Backbone.View.extend({
        tagName: 'li', // name of (orphan) root tag in 'this.el'
        initialize: function(){
          _.bindAll(this, 'render');  // every function that uses 'this' as the current object should be here
        },
    
        render: function(){
          $(this.el).html('<span>'+this.model.get('part1')+' '+this.model.get('part2')+'</span>');
          return this;  // for chainable calls, like .render().el 
        }
      });
    
      var ListView = Backbone.View.extend({  // our main app's view
        el: $('body'),  // attaches 'this.el' to an existing element
    
        events: {
            'click button#add': 'addItem'
        },
        
        initialize: function(){  // automatically call upon instantiation; do all bindings except for UI events (e.g. clicks)
          _.bindAll(this, 'render', 'addItem', 'appendItem'); // every function that uses 'this' as the current object should be here
    
          this.collection = new List();
          this.collection.bind('add', this.appendItem);  // collection event binder
    
          this.counter = 0;  // total number of items added so far
          this.render();  // not all views are self-rendering; this one is
        },
    
        render: function(){
          var self = this;  // save reference to 'this' so it can be accessed from within the scope of the callback
          $(this.el).append("<button id='add'>Add list item</button>");
          $(this.el).append("<ul></ul>");
          _(this.collection.models).each(function(item){
            // in case collection is not empty
            self.appendItem(item);
          }, this);
        },
    
        // addItem now works with just the models/collections
        addItem: function(){
          this.counter++;
          var item = new Item();
          item.set({
            part2: item.get('part2') + this.counter  // modify item defaults
          });
          this.collection.add(item);  // add item to collection; view is updated via event 'add'
        },
        
        appendItem: function(item){
          var itemView = new ItemView({
            model: item
          });
          $('ul', this.el).append(itemView.render().el);
        }
      });
      
      var listView = new ListView();  // instantiate main app view
    })(jQuery);

####Create Model actions

We use `Backbone.sync` to override persistence storage (so we can do `Model.destroy()`).  In our `ItemView` we now have two clickable events for each `Item` (swap and delete).  

    (function($){  // creates a self-executing wrapper
    
      // `Backbone.sync` allows use of `Model.destroy()` without raising an error
      Backbone.sync = function(method, model, success, error){
        success();
      }
    
      var Item = Backbone.Model.extend({
        defaults: {
            part1: 'hello',
            part2: 'world'
        }
      });
      
      var List = Backbone.Collection.extend({
        model: Item
      });
    
      var ItemView = Backbone.View.extend({
        tagName: 'li', // name of (orphan) root tag in 'this.el'; tag to be created
        
        // ItemView now respond to two clickable actions for each item `swap` and `delete`
        events: {
          'click span.swap': 'swap',
          'click span.delete': 'remove'
        },
    
        initialize: function(){
          _.bindAll(this, 'render', 'unrender', 'swap', 'remove');  // every function that uses 'this' as the current object should be here
    
          this.model.bind('change', this.render);
          this.model.bind('remove', this.unrender);
        },
    
        render: function(){
          $(this.el).html('<span style="color:black;">'+this.model.get('part1')+' '+this.model.get('part2')+'</span> &nbsp; &nbsp; <span class="swap" style="font-family:sans-serif; color:blue; cursor:pointer;">[swap]</span> <span class="delete" style="cursor:pointer; color:red; font-family:sans-serif;">[delete]</span>');
          return this;  // for chainable calls, like .render().el 
        },
    
        unrender: function(){
          $(this.el).remove();
        },
    
        swap: function(){
          var swapped = {
            part1: this.model.get('part2'),
            part2: this.model.get('part1')
          };
          this.model.set(swapped);
        },
    
        remove: function(){
          this.model.destroy();
        }
      });
    
      var ListView = Backbone.View.extend({  // our main app's view
        el: $('body'),  // attaches 'this.el' to an existing element
    
        events: {
            'click button#add': 'addItem'
        },
        
        initialize: function(){  // automatically call upon instantiation; do all bindings except for UI events (e.g. clicks)
          _.bindAll(this, 'render', 'addItem', 'appendItem'); // every function that uses 'this' as the current object should be here
    
          this.collection = new List();
          this.collection.bind('add', this.appendItem);  // collection event binder
    
          this.counter = 0;  // total number of items added so far
          this.render();  // not all views are self-rendering; this one is
        },
    
        render: function(){
          var self = this;  // save reference to 'this' so it can be accessed from within the scope of the callback
          $(this.el).append("<button id='add'>Add list item</button>");
          $(this.el).append("<ul></ul>");
          _(this.collection.models).each(function(item){
            // in case collection is not empty
            self.appendItem(item);
          }, this);
        },
    
        // addItem now works with just the models/collections
        addItem: function(){
          this.counter++;
          var item = new Item();
          item.set({
            part2: item.get('part2') + this.counter  // modify item defaults
          });
          this.collection.add(item);  // add item to collection; view is updated via event 'add'
        },
        
        appendItem: function(item){
          var itemView = new ItemView({
            model: item
          });
          $('ul', this.el).append(itemView.render().el);
        }
      });
      
      var listView = new ListView();  // instantiate main app view
    })(jQuery);

