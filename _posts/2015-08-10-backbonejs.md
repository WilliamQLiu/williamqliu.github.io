---
layout: post
title: Backbone.js 
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
*  [Setup](#setup)
*  [Dependencies](#dependencies)
*  [Basic Javascript](#js)
    - [Organize Data without Backbone](#jsorganizedata)
    - [Organize Data with Backbone](#jsorganizedatabackbone)
*  [Models](#models)
    - [`set()` and `get()`](#modelssetget) 
    - [`toJSON()`](#modelstojson)
    - [Validating Models](#modelsvalidate)
    - [Model id, cid, idAttribute](#modelsid)
*  [Collections](#collections)
    - [Collections (get, set)](#collectionsgetset)
    - [Collections (multiple Models)](#collectionsmultiple)
    - [Collections Underscore functions](#collectionsunderscore)
*  [Events Mixin](#events)
    - [Publish and Subscribe Pattern](#eventspushsub)
    - [Events Format](#eventsformat)
    - [`on` and `trigger`](#eventsontrigger)
    - [`off`](#eventsoff)
    - [Events trigger on error example](#eventsonerror)
*  [Views](#views)
    - [el](#viewsel)
    - [initialize](#viewsinitialize)
    - [render](#viewsrender)
    - [events](#viewsevents)
    - [template](#viewstemplate)
*  [Routes](#routes)
    - [History](#history)
*  [Non JS Template](#nonjstemplate)
*  [Hello World Example](#hwexample)
    - [1. Create View with initialize](#hwview)
    - [2. Bind DOM events to View methods](#hwevent)
    - [3. Models and Collections](#hwmodelcollection)
    - [4. Delegate Model to View](#hwmodelview)
    - [5. Create Model Actions](#hwmodelaction)
*  [API Integration](#apiintegration)
    - [API Mappings](#apimappings)
    - [API JSON](#apijson)
    - [API fetch()](#apifetch)
    - [API save()](#apisave)
    - [API destroy()](#apidestroy)
    - [API options](#apioptions)

##<a id="summary">Summary</a>

__Backbone.js__ is a JavaScript MV* for front-end web development.  It's basically a huge step up from pulling and pushing data (e.g. pull with AJAX GET, push with WebSockets using a persistent connection between client and server) to manipulate data (where you previously would have to write the glue code that looks at the DOM, finds an element with a specific id, and update the HTML manually).  Backbone is pre-configured to sync with a RESTful API and is great for building single page applications (don't have to wait for entire page to reload for every time we change the underlying data).

Note: For back-end/server stuff, Django is mainly a synchronous framework (a request goes in, we wait while the server computes stuff, then a response goes out).  If you need a persistent connection, change your back-end server to something like Tornado or Twisted (which complicates projects because of connections breaking).

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

##<a id="setup">Setup</a>

This is my particular setup:

`sudo npm install -g grunt-cli bower` - install global depenencies
`sudo npm install -g coveralls` - optionally install coveralls (for Travis CI)
`npm install` - to install NPM dependencies
`bower install` - to install Bower dependencies

####<a id="buildsystem">Build System with Grunt and Gulp</a>

The build system is used to build, preview, and test your project.

####<a id="packagemanager">Package Manager with Bower and npm</a>

The package manager is used for dependency management so you don't have to manually download and manage your scripts.

__Bower__ is a way of going to GitHub or a developer's site to download a .zip of a JavaScript tool.  You can `bower install [stuff]` and then the code is saved in a directory, usually `app/bower_components/`.  E.g. `bower install query` to install jQuery.

Example:

    $bower init
    # For a new project; I select 'globals' for types of modules to expose and this process creates a 'bower.json' file
    $bower search backbone.localstorage
    > Search results:  backbone.localStorage git://github.com/jeromegn/Backbone...
    $bower info backbone.localStorage
    $bower install backbone.localStorage --save
    $bower install  # installs all dependencies listed in 'bower.json'

When working with multiple developers, we want to include `--save` to tell Bower to remember this new dependency.

__Grunt__ is a JavaScript-based task runner that runs common jobs (e.g. `grunt server`, `grunt build`, `grunt test`).  Tasks are defined in a customized __Gruntfile.js__ file that lives in the root directory of your project.

*  `grunt build` sucks up your js scripts, concatenates them, minifies, and prefixes them with unique hashes to prevent browsers from caching outdated versions.
*  `grunt server` compiles any CoffeeScript js files, Sass CSS styles, then connects to your server and opens up an instance, usually at `localhost:9000` in your browser

####<a id="installother">Other Options (Sass, Less, RequireJS, Modernizer, CoffeeScript)</a>

__Sass__ (Syntactically Awesome Stylesheets) is an extension to CSS.  Sass allows CSS to have variables, support mixins (which are repeated code that you can insert into any location to make code DRY) and loops (`@for`, `@each`, and `@while`).

__Less__ is also another extension to CSS and like Sass, has many of the same features like variables and mixins.  Less is more like CSS than Sass.

__RequireJS__ is a JavaScript script loader to help improve the load time of multiple JavaScript files.  Synchronous file loading is slow and asynchronous file loading can have dependency issues.

__Modernizer__ runs on a web page load to detect whether certain features are available (to help support browsers that lag behind).  It lets you write conditional JavaScript and CSS code to handle each situation of whether a browser supports a feature or not.

__CoffeeScript__ is a language that compiles into JavaScript.

####<a id="yo">Scaffolds with Yo</a>

There's scripts out there like Yo that scaffolds out a new application, writing your build configuration (e.g. Gruntfile, Gulpfile) and pulling in the relevant build tasks and package manager dependencies (Bower, npm) that your project needs.

Yeoman includes the scaffolding tool (__yo__), the build tool (e.g. __Grunt__, __Gulp__), and the __package manager__ (e.g. __Bower__ and __npm__).

1. Install yo with: `npm install -yo`
2. To get some functional boilerplate Backbone app out of the box, we can do:  `npm install -g generator-backbone`
3. Make a new directory and `cd` into it: `mkdir myproject && cd $_`
4. Run `yo backbone [app-name]`.  Additionally for backbone, you can:
    -  `yo backbone:collection todos`: create a collection for our To Do items
    -  `yo backbone:model todo`: create a model for a To Do item
    -  `yo backbone:view todo`: create a view for just one To Do item.
    -  `yo backbone:router todo`: to create a router for your app

You'll normally see a structure like:

*  `app/` where your pure, non-compiled, non-minified source code goes
*  `app/scripts/` is where your JavaScript goes.
*  `app/styles/` is where your CSS goes
*  `app/index.html` is the non-minified version of `index.html`
*  `Gruntfile.js` has all the build, server, and test tasks defined

Note: I tried this out and it seemed a little too much so I installed piece by piece as needed.

##<a id="js">Basic JavaScript</a>

Here's a brief review of JavaScript.

####<a id="jsorganizedata">Organizing Data (without Backbone.js)</a>

This is how to represent and organize data using straight JavaScript.

_main.js_

    // Setup our Person Object, have our unique attributes to the Person
    var Person = function(config) {
        this.name = config.name;
        this.age = config.age;
        this.occupation = config.occupation;
    };
    
    // Common Traits/Methods for our Person Object
    Person.prototype.work = function() {
        return this.name + ' is working';
    }

_Chrome Dev Tools_

    var person = new Person({ name: 'Will Liu', age: 30, occupation: 'programmer'});  // undefined
    person.work();  // "Will Liu is working"

####<a id="jsorganizedatabackbone">Organizing Data (with Backbone.js)</a>

Same example above, but with Backbone.js.

_main.js_

    var Person = Backbone.Model.extend({
        defaults: {
            name: 'Will Liu',
            age: 30,
            occupation: 'programmer'
        },
        work: function() {
            return this.get('name') + ' is working';
        }
    });

_Chrome Dev Tools_

    var person = new Person;  // undefined
    person  // returns a lot more fields including: attributes, changed, cid as well as the original __proto__

We get additional details like:

*  `changed: Object` - determines if object was changed
*  `__proto__ : Object` - we still have access to our original prototype.
*  `attributes: Object`  - attributes (what would have been in _prototype_).  In order to access these attributes, we now need to use `.get()` and `.set()`

##<a id="models">Models</a>

Models contain interactive data and logic such as getters, setters, data initialization, and data validation.  Properties are dynamic (can be created on the spot) and don't have a specific type associated with it.

    var app = {};  // Create namespace for our app
    
    app.Todo = Backbone.Model.extend({
      defaults: {
          title: '',
          completed: false
      }
    });

To test Models in Chrome, you can go to the Console and do:

1. `var todo = new app.Todo({title: 'Learn Backbone.js', completed: false});`
2. `todo.get('title');` returns `"Learn Backbone.js"`
3. `todo.set('created_at', Date());` create a new object
4. `todo.get('created_at')` returns 'Tue Aug 11 2015'

####<a id="modelssetget">Models (`set()` and `get()`)</a>

Instead of modifying the Model attributes directly, make sure to use Backbone's `set()` and `get()`.  If you're setting one attribute, you can just pass in the value.  If you're setting more than one attribute, pass in the object (i.e. `{}`).  Note: `set()` updates the DOM while triggering the 'change' event while `save()` updates the Database.

    person.get('occupation');  // "programmer"
    person.set('occupation': 'developer');  // setting single attribute
    person.set({name:'Laura Summers', occupation: 'designer'});  // setting multiple attributes

What really goes on behind the scenes is that when you do something like a set or get, you're sending data through a 'request payload' (that you can see in the Chrome Tools under > Network to see the most recent requests); this 'request payload' takes and stringifies our JSON data.

####<a id="modelstojson">Models (`toJSON()`)</a>

We can take our Model objects and call `toJSON()`, which will return a JavaScript object.

     person.toJSON();  // returns Object with attributes (age, name, occupation) and __proto__

####<a id="modelsvalidate">Models (validate)</a>

Backbone has model validation using `model.validate()`, which checks the attribute values before setting them.  By default, this occurs during a `save()` method or when `set()` is called if `{validate: true}` is passed as an argument.

    var Person = new Backbone.Model({name: 'Will'});
    
    // Validate the model name
    Person.validate = function(attrs) {
      if (attrs.age <0) {
        return 'Age must be positive';
      }
    
      if (!attrs.name) {
        return 'Everyone needs a name';
      }
    };
    
    // Change the name
    Person.set({name: 'Laura'});
    console.log(Person.get('name'));  // 'Laura'
    
    // Remove the name attribute, force validation
    Person.unset('name', {validate: true});  // false

####<a id="modelsid">Models (id, cid, idAttribute)</a>

When you exchange data between the client and the server, you need a way to uniquely identify models.  In Backbone, we have the `id`, `cid`, and `idAttribute` properties.

-  Each model has an `id`, a unique identifier that is either an integer or a string (e.g. a UUID).
-  Each model also has a client id `cid`, which is automatically generated by Backbone when the model is created.
-  You can use either the `id` or the `cid` to retrieve a model from a collection.  The difference is that the `cid` is generated by Backbone, which is helpful when you don't have a true id yet (e.g. if your model hasn't been saved to the server or you aren't saving it to a database).
-  The `idAttribute` is the id from the server (i.e. the `id` in your database).  This is like a mapper.  By default, it assumes `id`.

##<a id="events">Events (an object mixin)</a>

Events is an object mixin (like a class mixin) that you can use to listen for well... events across the other Backbone objects like Model, Collection, Router, History, and View.  So how does `Backbone.Events` give any object the ability to bind and trigger custom events?

1. What normally happens is you have a function that calls another function by name.  The programmer controls the events; the first function runs then the second function.
2. However, Events works by replacing the first function with an event handler (i.e. a specific function that looks for a specific event to occur) that when triggered, runs the second function.

Events have a few methods we're interested in like `on`, `off`, and `trigger` (similar to jQuery).  `on` has the format `object.on(event, callback, [context])`, which means to bind an object to an event and a callback.  When that event is triggered, it executes the callback.  E.g. `todoList.on('add', this.addAll, this);` would mean everytime a new item is 'add'ed to the Backbone.Collection, the event 'add' is triggered.  You can add events to Collections, Views, etc.

So how does this work in practice?  Imagine that you update your Model's data, but the data isn't updated on your screen.  With Events, you can easily setup an event to look for changes on the Model's data and if there is a change, run a function to rerender the View.  Now the user sees data changes immediately on their screen.

####<a id="eventspushsub">Events (Publish/Subscribe Pattern)</a>

Events follow the publisher/subscriber behavior where publishers (aka senders/messages) do not sent messages directly to specific subscribers (aka receivers) and instead are sent to a message-oriented middleware system.  More specifically, events follow the __observer pattern__ where an object (the subject) has a list of dependents called observers that are notified automatically of any state changes (usually by calling one of their methods).

####<a id="eventsformat">Events (Format)</a>

Events have the following format.

    //{"<EVENT_TYPE> <ELEMENT_ID>": "<CALLBACK_FUNCTION>"}
    //e.g. events: {'keypress #new-todo': 'createTodoOnEnter'}
    // The jQuery equivalent would be `$('#new-todo').keypress(createTodoOnEnter);`
    
    App.Views.Advice = Backbone.View.extend({
        events: {
            'click': 'showAlert'
        },
        showAlert: function() {
            alsert('you clicked me!');
        }
    });

####<a id="eventsformat">Events (Custom Events)</a>

Events can also have a simpler format and allows custom event triggers with: `object.on({click: action})`.  We can trigger on any event (e.g. on Model's data changing) or a specific event (e.g. on change of a specific field in the Model's data).

    var Todo = Backbone.Model.extend({
      defaults: {
        title: '',
        completed: false
      }
    });
    
    var myTodo = new Todo();
    myTodo.set({title: 'Buy some cookies', completed:true});
    
    // Run custom function if change on specific fields
    myTodo.on({
      'change:title': titleChanged,
      'change:completed': stateChanged
    });
    
    function titleChanged(){
      console.log('Title changed!');
    }
    
    function stateChanged(){
      console.log('State changed!');
    }
    
    myTodo.set({title: 'Get the groceries!'});  // Title changed!

The general format is:

    object.on("change: something", function(stuff){ 
      console.log("Stuff");
    });`

####<a id="eventsontrigger">Events as a Mixin using `on` and `trigger`</a>

Let's get into more detail with how `on` and `trigger` can be used for custom events.

    var ourObject = {};
    
    // Mixin
    _.extend(ourObject, Backbone.Events);
    
    // Add a custom event
    ourObject.on('dance', function(msg){
      console.log('We triggered ' + msg);
    });
    
    // Trigger the custom event
    ourObject.trigger('dance', 'ourevent');

The `on` is similar to the __subscribe__ and `trigger` is similar to __publish__ in a Publish/Subscribe pattern.  `on` binds a callback function to an object 'dance'.  The callback is invoked whenever the event is triggered.  If you do a lot of events, consider namespacing the events.  If there is no listener for a trigger (e.g. `object.trigger("jump", "jump.  Yeah!"`, then nothing happens.

    var ourObject = {};
    
    // Mixin
    _.extend(ourObject, Backbone.Events);
    
    function dancing(msg) {
      console.log("We started " + msg);
    }
    
    // Add namespaced custom events
    ourObject.on("dance:tap", dancing);
    ourObject.on("dance:break", dancing);
    
    // Trigger the custom events individually
    ourObject.trigger("dance:tap", "tap dancing. Yeahhh!");
    ourObject.trigger("dance:break", "break dancing.  Woottt!");

Instead of a single event, we can also trigger multiple events / pass multiple arguments to the callback function.

    // Trigger multiple events
    ourObject.trigger("dance:tap dance:break", " dancing!");

####<a id="eventsonerror">Events - on error example</a> 

A common example is listening for a triggered event on error:

     person.on('error', function(model, error) {
        console.log(error);
    });

####<a id="eventsall">Events as a Mixin using `all`</a>

The event `all` creates notifications for every event that occurs on the object.

    var ourObject = {};
    
    // Mixin
    _.extend(ourObject, Backbone.Events);
    
    function dancing(msg){
      console.log("We started " + msg);
    }
    
    ourObject.on("all", function(eventName){
      console.log("The name of the event passed was " + eventName);
    });
    
    // Each event will be caught with a catch 'all' event listener
    ourObject.trigger("dance:tap", "tap dancing.  Yeahhh!");
    ourObject.trigger("dance:break", "break dancing.  Woottt!");

####<a id="eventsoff">Events as a Mixin using `off`</a>

`off` removes callback functions that were previously bound to an object.  In a Publish/Subscribe pattern, `off` is similar to __unsubscribe__.  We can remove specific callbacks to an event or we can remove all callbacks for the event.

    // Removes a single event bound to the object
    ourObject.off("dance:tap");

Every `on` should have an `off` or else we'll have memory leaks.  You should use `on`/`off` on views and their corresponding models at the same time, otherwise if you remove a view that you registered to be notified about events on a model, but don't remove the model or call `off` to remove the view's event handler, the view's callback function can't be collected by JavaScript's garbage collector.

####<a id="eventsoff">Events using `listenTo()` and `stopListening()`</a>

We can either have every `on` called on an object to also have an `off` called, or we can use `listenTo()`, which allows Views to bind to Model notifications and unbind from all of them with just one call `stopListening()`.  The default `View.remove()` makes a call to `stopListening()` to ensure that any listeners bound are unbound before the view is destroyed.

    var view = new Backbone.View();
    var b = _.extend({}, Backbone.Events);
    
    view.listenTo(b, 'all', function(){
      console.log(true);
    });
    b.trigger('anything');  // logs: true
    
    view.listenTo(b, 'all', function(){
      console.log(false);
    });
    view.remove();  // 'stopListening()' implicitly called by 'remove()'
    b.trigger('anything');  // does not log anything since trigger has been removed

####<a id="eventsviews">Events binding to Views</a>

Events bind to views a little differently; see the Views - Events section.

##<a id="views">Views (Overview)</a>

Views are the user interface and tells us how data is displayed.  Backbone views are generally based on View Templates.  While rendering the UI, Views also listen to the events from the DOM.  Views have four basic properties (with an optional fifth 'template'):

1. `el`
2. `initialize`
3. `render`
4. `events`
5. `template`

####<a id="viewsel">Views (el)</a>

The el property stands for element and is the reference to the DOM.  Every view has an element associated with it where the view will inject content in.  `el` tells you how to inject the content.  Without Backbone, you would manually write out the HTML string in your Javascript, which is a code smell.  Backbone's `el` will allow you to do this cleanly.

A few things to note:

- A view has the following properties:
    +  `el` - where you want to start inserting your HTML; `this.el` is created from the view's `el`.  You use 'el' or 'tagName'
    +  `tagName` - can specify your HTML's wrapper; default is a `<div>` wrapper.  You use 'el' or 'tagName'.
    +  `className` - can specify your HTML's class
    +  `id` - can specify your HTML's id
    +  `attributes`
    +  If none of the above are specified, then it is an empty `div`.
- `view.$el` is the cached jQuery object of the view's element (`view.el`)

__Example 1 - view with default values__

We create a simple view, which defaults `el` to a `<div>`.

_main.js_

    var Person = Backbone.Model.extend({
        defaults: {
            name: 'Will Liu',
            age: 30,
            occupation: 'programmer'
        }
    });
    
    var PersonView = Backbone.View.extend({
    });
    var personView = new PersonView;

_Chrome Dev Tool_

    >personView.el  // this element
     <div></div>
    >personView.$el  // this element wrapped in jQuery
     [<div></div>]

__Example 2 - view with different tagName, className, and id__

_main.js_

You can specify your own tagName (i.e. instead of a `<div>`), className

    var PersonView = Backbone.View.extend({
        tagName: 'li',
        className: 'myperson',
        id: 'myid'
    });

_Chrome Dev Tool_

    >var personView = new PersonView();
    >personView.$el
    [<li id="myid" class="myperson"></li>]

####<a id="viewsinitialize">Views (initialize)</a>

`initialize` - the first function (automatically called) when a view is instantiated.  You can pass parameters that will be attached to a model, collection or `view.el`

    var PersonView = Backbone.View.extend({
        initialize: function() {
            console.log('hi there, I initialized');
        }
    })

####<a id="viewsrender">Views (render)</a>

`render` - this function injects the markup into the elements.  This is not required; some views can call other view's render function.

    var PersonView = Backbone.View.extend({
        tagName: 'li',    
    
        initialize: function() {
            console.log('hi there, I initialized');
            this.render();  // You can render immediately
        }
        render: function() {
            this.$el.html(this.model.get('name'));  // the jQuery wrapped item
        }
    });
    
    var person = new Person;  // Create the Model
    var personView = new PersonView({model: person})  // Create the View, pass in the Model


####<a id="viewsevents">Views (events mixin)</a>

Events bind to Views a little different; in Views there's two types of events you can listen for and depending on the type, `this` points to different things.  The two types of events you can listen for are:

1. DOM events
2. Events triggered using the Event API

__Dom events__ can be bound using the View's `events` property or using `jQuery.on()`.

*  If the event is bound using the `events` property, then `this` refers to the View object
*  If the event is bound using jQuery, then `this` is set to the handling DOM element by jQuery.

All Dom event callbacks are passed as an `event` object by jQuery.

__Event API__ events can be bound using `on()` on the observed object with a context parameter passed as the third parameter or with `listenTo()`.

*  If the event is bound using `on()` on the object, then a context parameter is passed as the third argument.
*  If the event is bound using `listenTo()` then within the callback `this` refers to the listener.  The arguments passed to Event API callbacks depends on the type of event (see Catalog of Events in the Backbonejs documentation).

For example:

    <div id="todo">
      <input type='checkbox'/>
    </div>
    
    var View = Backbone.View.extend({
      el: '#todo',
    
      // bind to DOM event using the `events` property
      events: {
        'click [type="checkbox"]': 'clicked',
      },
      initialize: function() {
        // bind to DOM event using jQuery
        this.$el.click(this.jqueryClicked);
        
        // bind to an Event API
        this.on('apiEvent', this.callback);        
      },
      
      // 'this' is view
      clicked: function(event) {
        console.log("events handled for " + this.el.outerHTML);
        this.trigger('apiEvent', event.type);
      },
      
      // 'this' is handling a DOM element
      jQueryClicked: function(event) {
        console.log("jQuery handler for " + this.outerHTML);
      },
      
      callback: function(eventType) {
        console.log("event type was " + eventType);
      }
    });
    var view = new View();


####<a id="viewstemplate">Views (template)</a>

Instead of rendering HTML manually in our View's `render` function, you can use templates.  Templates can have different types including the default Underscore (`_`) to Mustache.

`template` - _.js templates have the following format: `_.template(templateString, [data], [settings])` where in `templateString` you can use the placeholders to dynamically insert data:
*  `<%= %>` (allows for HTML escape)
*  `<%- %>` (does not allow for HTML escape)
*  `<% %>` runs any javascript code

####<a id="viewsinlinetemplate">Views (inline template)</a>

You can do an inline template.  The View is rendered , we wrap the `el` tags (`li`) to html, and then use an inline template.

_main.js_

    var AdviceModel = Backbone.Model.extend({
        defaults: {
            skills: [],
            title: "default_title",
            location: "New York, NY",
            img: null,
            is_active: true,
            q_who_what: "who and what",
            q_avg_day: "avg day",
            q_like_dislike: "like dislike",
            q_advice_new: "advice new",
            q_big_accomplish: "big accomplish",
            flagged: 0
        },
    });
    
    var AdviceView = Backbone.View.extend({
        tagName: 'li',
        template: _.template("<%= q_who_what %> at <%= location %>"),
    
        initialize: function() {
            console.log('View is getting initialized');
            console.log("Model is ", this.model);
            this.render();
        },
    
        render: function() {
            console.log('Rendering View');
            //this.$el.html( this.model.get('title'));  // get the model's  attribute title
            console.log("Model in JSON is ", this.model.toJSON());
            this.$el.html( this.template( this.model.toJSON()));
        }
    });
    
    var advice = new AdviceModel;
    var adviceView = new AdviceView({model: advice});
    //This renders in Chrome: 'who and what at New York, NY'

####<a id="viewsexternaltemplate">Views (external template)</a>

We can place our template into an external script.

_template.html_

    <!-- Our External Template -->
    <script id="adviceTemplate" type="text/template">
        <%= q_who_what %> at <%= location %>
    </script>

_main.js_

    var AdviceModel = Backbone.Model.extend({
            defaults: {
                skills: [],
                title: "default_title",
                location: "New York, NY",
                img: null,
                is_active: true,
                q_who_what: "who and what",
                q_avg_day: "avg day",
                q_like_dislike: "like dislike",
                q_advice_new: "advice new",
                q_big_accomplish: "big accomplish",
                flagged: 0
            },
        });
    
    var AdviceView = Backbone.View.extend({
        tagName: 'li',
        template: _.template( $('#adviceTemplate').html() ),
    
        initialize: function() {
            console.log('View is getting initialized');
            console.log("Model is ", this.model);
            this.render();
        },
    
        render: function() {
            console.log('Rendering View');
            //this.$el.html( this.model.get('title'));  // get the model's  attribute title
            console.log("Model in JSON is ", this.model.toJSON());
            this.$el.html( this.template( this.model.toJSON()));
        }
    });
    
    var advice = new AdviceModel;
    var adviceView = new AdviceView({model: advice});
    //This renders in Chrome: 'who and what at New York, NY'

##<a id="collections">Collections</a>

Collections are ordered sets of models.  You can get and set models in the collection, listen for events when any element in the collection changes, and fetch data from the server and save the model's data to the database.  Collections require a reference, usually a __url__ parameter where the model's resource is located on the server (e.g. on an API).  If you want to work with local files, you can use the backbone.localStorage-min.js file.

    app.TodoList = Backbone.Collection.extend({
      model: app.Todo,
      //localStorage: new Store("backbone-todo")
    });
    app.todoList = new app.TodoList();  // instance of the Collection

To test Collections in Chrome, you can go to the Console and do:

1. `var todoList = new app.TodoList()`
2. `todoList.create({title: 'Learn Backbone\s Collection'});`
3. `var lmodel = new app.Todo({title: 'Learn Models', completed: true});`
4. `todoList.add(lmodel);`
5. `todoList.pluck('title');`  // ["Learn Backbone's Collection", "Learn Models"]
6. `todoList.pluck('completed');`  // [false, true]
7. `JSON.stringify(todoList);`  // "[{"title":"Learn Backbone's Collection", "completed":false"}..., {"title":"Learn Models"...}]"

####<a id="collectionsnobackbone">Collections (trying with pure JavaScript)</a>

To really get an idea behind what Collections do, let's think about what we would do without them.  We now create multiple models and multiple views.

    var person = new Person;
    var personView = new PersonView({model: person});
    
    var person2 = new Person({name: 'Will Liu', age: 30});
    var personView2 = new PersonView({model: person2});
    
    // This gets messy quick
    var people = [personView, personView2]  // creates an array of people, but we don't have helpful events like add, delete

We're really missing out on helper functions like add, delete, sync.

####<a id="collectionstojson">Collections (trying with Backbone.js)</a>

Now let's try this with Backbone.js' Collections.

_Adding in Models to Collections_

    var person = new Person;
    var person2 = new Person({name: 'Will Liu', age: 30});
    
    var PeopleCollection = Backbone.Collection.extend({
        model: Person  // reference the Model instead of the instance
    });
    
    var peopleCollection = new PeopleCollection;
    peopleCollection.add(person);
    console.log(peopleCollection);  // now length is 1, the model is contained in the 'models' attribute

_Adding in individual objects (that represent the Models) to Collections_

    var peopleCollection = new PeopleCollection([
      {
        name: 'Will Liu',
        age: 30,
        occupation: 'programmer'
      },
      {
        name: 'Laura Summers',
        age: 27,
        occupation: 'designer'
      }
    ]);

Backbone understands that these items passed are actually Models since we declared this in our initial Collections.

####<a id="collectionsgetset">Collections (`get()`, `set()`)</a>

By default, the Models stored inside Collections are enumerated (i.e. ordered) by their `id` property.  To get a specific model, you can use `collection.get(id)`, which will check the array for the existence of the model with the right `id`.  You can also try to reference a model with the `cid` or `idAttribute`.  This also applies to `collection.set(id)`

####<a id="collectionstojson">Collections (`toJSON()`)</a>

    var PeopleCollection = Backbone.Collection.extend({
        model: People,
    });
    
    var peopleCollection = new PeopleCollection;
    console.log(peopleCollection);
    peopleCollection.toJSON();  // returns an array of our models

####<a id="collectionsmultiple">Collections (multiple Models)</a>

You can do the standard `collection.get()`, `collection.set()`, `collection.reset()` to affect the entire Collection instead of just a Model at a time.

####<a id="collectionsunderscore">Collections Underscore functions (forEach, sortBy(), map(), chain())</a>s

Since Backbone has a hard dependency on Underscore, you can use Underscore's utility functions directly on collections.  These include:

-  `forEach`: iterate over collections
-  `sortBy()`: sort a collection on a specific attribute
-  `map()`: iterate through a collection, map each value
-  `min()/max()`: retrieve an item with min or max value of an attribute
-  `filter()`: filter a collection (e.g. contains these ids)
-  `indexOf()`: return index of a particular item within a collection
-  `any()`: confirm if any of the values in a collection pass an iterator truth test
-  `size`: return size of a collection
-  `isEmpty()`: determines if a collection is empty
-  `keys()/values()`: gets a list of attribute names/keys and values

You can also use Underscore's `chain()` method to chain multiple methods (e.g. if you want to filter by age and map what is returned to another list)

##<a id="routes">Routes</a>

Routes reference a certain 'state' of the web application in the URL.  Routes are hash maps that match URL patterns to functions.  You can use parameter parts such as `todo/:id` or splats `file/*path` to match all the parameters from the splat on (make sure to make splat parameters last since they're greedy).  Remember that this navigates to a `#` location (e.g. a route to "about" leads to a url of "#about"). 

    // Example Route:  http://example.com/$search/hotels/page1
    
    var TodoRouter = Backbone.Router.extend({
      // define the route and funtion maps for this router
      routes: {
        "": "index",
        "about" : "showAbout",  // E.g. http://example.com/#about
        "todo/:id" : "getTodo",  // Using a ':param' variable; E.g. http://example.com/#todo/5
        "todos/*documentPath" : "downloadDocument", // Using a '*splat'; E.g. http://example.com/#todos/Meeting_schedule.doc
        "*other" : "defaultRoute",
      },
    
      index: function() {
        console.log("Hi, this is the index page");
      },      
      showAbout: function(){
        console.log("This is the showAbout page");
      },    
      getTodo: function(id){
        console.log("You reached todo item " + id);
      },    
      downloadDocument: function(id, path){
      },    
      defaultRoute: function(other){
        console.log("Invalid, you attempted to reach:" + other);
      }
    });

####<a id="routeseventtriggers">Routes with Custom Event Triggers</a>

You route with a custom event trigger.

    var vent = _.extend({}, Backbone.Events);
    
    App.Views.Appointment = Backbone.View.extend({
        initialize: function() {
            vent.on('appointment:show', this.show, this);
        },
        show: function(id) {
            console.log('Showing the appointment ' + id);
        }
    });
    
    App.Router = Backbone.Router.extend({
        routes: {
            '': 'index',
            'appointment/:id': 'showAppointment'
        },
        index: function() {
            console.log('hi, this is the index page');
        },
        showAppointment: function(appointmentId) {
            vent.trigger('appointment:show', appointmentId);
        }
    });
    
    new App.Views.Appointment;
    
    new App.Router;
    Backbone.history.start();    

The route matches with the url, which then triggers the appointment event.  The event calls the function 'show' which shows the appointment data (model or collection).

####<a id="history">History</a>

Backbone.history handles `hashchange` events in our application; this automatically handles routes that have been defined and trigger callbacks when they've been accessed.  Place this right after your Routes initialization.

    Backbone.history.start();

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
      <script>
        //Put Backbone.js stuff here or link to src
      </script>
    </body>
    </html>

##<a id="hwexample">Hello World Example</a>

####<a id="hwview">Declaration and Instantiation of a minimalist View with `initialize` and `render`</a>

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

####<a id="hwevent">Binding of DOM events to View methods with `events`</a>

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

####<a id="hwmodelcollection">How to use a Collection of Models to store data and tie changes to a View</a>

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

####<a id="hwmodelview">Delegate rendering of a Model to a dedicated View</a>

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

####<a id="hwmodelaction">Create Model actions</a>

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

##<a id="apiintegration">API Integration</a>

One of Backbone's biggest advantages is that it is pre-configured to sync with a RESTful API.  The API talks through a __Collection__ by specifying the __url__ of your resource endpoint and calling `fetch()` to get your data.  Once you get your data, you can do GET, POST, DEL, etc.

    var Books = Backbone.Collection.extend({
      url: '/books'
    });

####<a id="apimappings">API (Mappings)</a>

Here's how the __Collection__ and __Model__ components map to __REST__ resources.

    GET  /books/  ... collection.fetch();
    POST /books/  ... collection.create();
    GET  /books/1 ... model.fetch();
    PUT  /books/1 ... model.save();
    DEL  /books/1 ... model.destroy();

####<a id="apijson">API (JSON data)</a>

When fetching raw JSON data from an API, __Collection__ populates itself with data as an __array__ while __Model__ populates itself with data as an __object__.

    [{"id": 1}, {"id":2}]     ... a Collection with a couple of items
    {"id": 1, "name": "Will"} ... a Model with a couple of attributes

####<a id="apifetch">API - `Collections.fetch()`</a>

`Collections.fetch()` is used to fetch an entire collection of models from a server as a JSON array by sending a HTTP GET request to the URL specified by the collection's __url__ property.

    // Create Model
    var Todo = Backbone.Model.extend({
      defaults: {
        title: '',
        completed: false
      }
    });
    
    // Create Collection
    var TodosCollection = Backbone.Collection.extend({
      model: Todo,
      url: '/todos'  // specify 'url' for the HTTP request
    });
    
    var todos = new TodosCollection();  // instantiate
    todos.fetch();  // sends HTTP GET to '/todos'

Note: it might be a good idea to do `todos.fetch().then()` to specify what to do after `fetch()` is completed.

####<a id="apisave">API - `save()` and `Collections.create()`</a>

With `fetch()` we were able to get an entire collection of models, but we cannot save models individually (instead of as a group).  When we save a model, we call on the model's `save()` method, which checks if the model is a new instance:

1. If the model is a new instance that was created in the browser (i.e. does not have an id), then an HTTP POST is sent to the collection's URL.
2. If the model is not a new instance (i.e. has an id already), we construct a URL by appending the model's id to the collections URL and sends and a HTTP POST is sent to the server.

Note: If we want to save time, we can use `Collection.create()` to create a new model, add it to the collection, and send it to the server in a single method call.

    // Create Model
      var Todo = Backbone.Model.extend({
        defaults: {
          title: '',
          completed: false
        }
      });
      
    // Create Collection
    var TodosCollection = Backbone.Collection.extend({
      model: Todo,
      url: '/todos'  // specify 'url' for the HTTP request
    });
    
    var todos = new TodosCollection();  // instantiate
    todos.fetch();  // sends HTTP GET to '/todos'
    
    var todo2 = todos.get(2);
    todo2.set('title', 'go fishing');
    todo2.set('completed': true);
    todo2.save();  // sends HTTP PUT to '/todos/2'

Note: When a model is `saved()`, the `validate()` method will automatically be called and will trigger an `invalid` event if the model validation fails.

####<a id="apiparse">API - parse</a>

If the API does not return data in a format you like, you can specify the `parse()` function.

####<a id="apidestroy">API - `destroy()` and `Collections.remove()`</a>

A model can be deleted from the collection and server by calling `destroy()`.  `destroy()` sends an HTTP DELETE to the collection's URL.  If you want to only remove a model from a collection (and not delete it on the server), you can use `Collection.remove()`.

    // Create Model
      var Todo = Backbone.Model.extend({
        defaults: {
          title: '',
          completed: false
        }
      });
      
    // Create Collection
    var TodosCollection = Backbone.Collection.extend({
      model: Todo,
      url: '/todos'  // specify 'url' for the HTTP request
    });
    
    var todos = new TodosCollection();  // instantiate
    todos.fetch();  // sends HTTP GET to '/todos'
    
    var todo2 = todos.get(2);
    todo2.destroy();  // sends HTTP DELETE to '/todos/2' and removes from collection

####<a id="apioptions">API options</a>

Each RESTful API method accepts a variety of options, including success and error callbacks.  See the Backbone.js documentation for full descriptions of supported options.

    // Save partial information using HTTP PATCH by using API options
    model.clear().set({id: 1, a: 1, b: 2, c: 3, d: 4});
    model.save();
    model.save({b: 2, d:4}, {patch: true});
    console.log(this.syncArgs.method);  // 'patch'

####<a id="apiprimer">API Primer</a>

From the creator of Backbone.js, this is the best resource on how to really work with Models and Collections.

https://github.com/jashkenas/backbone/wiki/Backbone,-The-Primer



