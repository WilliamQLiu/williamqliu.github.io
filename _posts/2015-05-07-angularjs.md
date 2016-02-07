---
layout: post
title: AngularJS
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
    -  [Why Angular](#whyangular)
    -  [Step through of an App](#stepthrough)
    -  [High Level Structure of an App](#structure)
    -  [Comparisons to other JavaScript libraries/frameworks](#comparisons)
*  [Up and Running](#upandrunning)
    -  [New Project Setup](#newprojectsetup)
    -  


- - - -

##<a id="summary">Summary</a>

AngularJS is a client side web framework in JavaScript.  We break things down into __View__ (UI), __Controller__ (business logic), and __ViewModel__ (__$scope__).  We make things modular by using a __module__ with a __config__ file (`angular.module().config([])`).  The __config__ file goes to __routes__, which then splits into the above __View__ and __Controller__.

####<a id="whyangular">Why Angular</a>

AngularJS is a JavaScript framework instead of a library (think lots of structure).  Angular provides a higher level abstraction with a particular focus on CRUD (as opposed to making something like Games or GUI editors).

*  A __library__ is a collection of functions that you can call whenever you see fit (e.g. jQuery, backbone)
*  A __framework__ is a particular implementation of a web application with built-in data binding, decouples DOM manipulation to improve testability, opinionated directory layout

####<a id="stepthrough">Step through of an App</a>

What happens when an Angular app loads?

On our browser, when we visit a web site, say we load a html page (aka __template__) that has some angular markup.  What angular does in the background is it quickly parses the angular markup from the template using the __compiler__ and first searches for an `ng-app` directive.  That element and any child elements will then be handled with AngularJS.  The compiler basically teaches the browser new behaviors for these elements.  With the new features, the DOM is created and we call this a __view__.

So how is this different?  In a standard multi-page web site and some single page applications, the server creates the HTML and joins the data on the server, then sends this finished response to the client browser.  With Angular, the _template_ and the _data_ is sent to the browser and it gets assembled there using the compiler.  Neat huh?  The processing isn't done mostly on the server anymore, but instead on the client's browser.

####<a id="structure">High Level Structure of an AngularJS App</a>

So what's the pieces of an Angular app?  Angularjs follows the Model-View-Controller Pattern to structure a website; this structure decouples the code and separates concerns.  A user interacts with the __Controller__, which then manipulates the __Model__, which then updates the __View__ that is presented to the user.  The pieces are:

*  The __Model__ is the representation of the data.  This is instantiated from the Controller.  When data binding occurs, the data is synced between the Model and the View.  We use `ng-model` to get input fields and pass this back to the Controller.
*  The __View__ is the projection of the model through the HTML template (e.g. app/index.html).  When the Model is updated, the View is updated.  Views are made up of:
  *  __Directives__ extend regular HTML with custom functionality.  Most of the time these are used to create UI components.  There are many different directives including `ng-repeat`, `ng-controller`, `ng-bind`, `ng-app`, `ng-change`, `ng-model`, `ng-submit`, `ng-click`.  You see these in the html, like: `<html ng-app>`, which represents an Angular directive named 'ng-app'.
  *  __Expressions__ are variables placed inside data bindings (`{{}}`).  E.g. `{{ phone.name }}`.  Note: AngularJS is slightly different than JavaScript expressions.
*  The __Controller__ is the business logic behind the views.  The Controller's job is to establish the data binding between the Model and the View by exposing variables and functions to expressions and directives.  The Controller uses the `ng-controller` directive.  It's just a constructor function that takes in a `$` parameter, mainly the `$scope` parameter.  There are additional parameters including $https, $locationUrl, etc.

####<a id="comparisons">Comparisons to other JS Libraries and Frameworks</a>

*  There is a high level of abstraction; we don't have to pepper the HTML with classes or ids to identify where to attach event listeners.
*  We don't have to register any event listeners or write callbacks

- - - -

##Up and Running

Here's a quick summary of how to get up and running with AngularJS.

*  Make folder
*  `yo angular <appname>`
    -  No to Gulp, No to SASS, Yes to Bootstrap
    -  e.g. `yo angular:app myAppName`
    -  If this installs in the wrong folder location, then check your `.yo-rc.json` file (delete it), then recreate in the right folder location
*  `bower install --save <some js library (e.g. firebase)>`
*  `grunt serve` and then `grunt build`





##Module

__Modules__ are a way to package relevant code under a single name.  Each module creates its own controllers, services, directives.  The first argument is the module name.  You specify a second argument only if we're looking up an existing module.  If you want to create a new module, don't add in a second argument.  For example, below we call AngularJS to look up a module name (of store) and then we place our dependencies in the [].  To create a module, we use the __ng-app__ directive.

###Example: app.js

    var app = angular.module('store', [ ]);

We then call this in our html under:

###Example: index.html

    <html ng-app="store">
    <body>
      <script type="text/javascript" src="app.js"></script>

- - - -

##Data Binding

You can do one-way or two-way data binding.  To get data back from an `ng-model` (e.g. `<input type="text" ng-model="name">`, you can use `ng-bind` or `{{ }}` like the below example: 

    <h1>Hi <span ng-bind="name"></span></h1>
    <h1>Hi {{ name }}</h1>

By default, we have two-way data binding.  We may want one-way data binding in case we have a lot of elements on the screen and know only to update specific elements while leaving the rest alone (e.g. we know username won't change).  We use `::` to one-way data binding:

    <div ng-repeat="note in ctrl.notes">
        Without Bind Once: <div class="label" ng-bind="note.label"></div>
        With Bind Once: <div class="label" ng-bind="::note.label"></div>
    </div>

##Controllers

__Controllers__ are the business logic that goes in between the Model and the View.  This means that controllers are the JavaScript functions that perform or trigger our __UI-oriented__ work (non UI-oriented work is under __Services__.  This could be fetching data from the server, deciding what to display to the user, user interactions like when someone clicks a button or text validation.

    <html ng-app="notesApp">
    <head><title>Notes App</title></head>
    <body ng-controller="MainCtrl as ctrl">
      {{ctrl.helloMsg}} AngularJS.
      <br/>
      {{ctrl.goodbyeMsg}} AngularJS
    
    <script
      src="/Users/williamliu/GitHub/js-examples/angular/angular.min.js">
    </script>
    <script type="text/javascript">
      angular.module('notesApp', [])
        .controller('MainCtrl', [function() {
           this.helloMsg = 'Hello ';
           this.goodbyeMsg = 'Goodbye ';
      }]);
    </script>
    </body>
    </html>

Main things to note are:

*  To use a controller, we start `ng-controller`
*  We can give a Controller a shorter name `MainCtrl as ctrl` and use as `{{ ctrl.helloMsg }}`
*  We can use the controller function `.controller` on the module (`notesApp`)
*  The controller uses `this` to get data and expose data from the controller to the UI.
*  Whatever the user needs to see, use `this`, otherwise use a local variable in the controller function (so that it is not exposed to users)
*  As good practice, we should save `this` to var `self`.  See below example.

###Controller Example

    <!-- File: chapter2/controller-click-message.html -->
    <html ng-app="notesApp">
    <head><title>Notes App</title></head>
    <body ng-controller="MainCtrl as ctrl">
      {{ctrl.message}} AngularJS.
    
      <button ng-click="ctrl.changeMessage()">
        Change Message
      </button>
    <script
      src="/Users/williamliu/GitHub/js-examples/angular/angular.min.js">
    </script>
    <script type="text/javascript">
      angular.module('notesApp', [])
        .controller('MainCtrl', [function() {
          var self = this;
          self.message= 'Hello ';
          self.changeMessage = function() {
            self.message = 'Goodbye';
          };
      }]);
    </script>
    </body>
    </html>

##Directives

*  `ng-app` is the main directive, used to tell where its okay to use AngularJS
*  `ng-model` used to get data from HTML and make available in JS
*  `ng-bind` used to put variable from JS back to HTML; also same as `{{ }}`
*  `ng-show` and `ng-hide` used to show and hide HTML elements
*  `ng-class` used to apply and remove CSS classes 
*  `ng-repeat` used to repeat elements; has helper functions (e.g. `$first`, `$index`) and can tell if rendering unique like with `track by`
*  `ng-repeat-start` and `ng-repeat-end` to get detailed custom `ng-repeat`
*  `ng-click` used to detect click and if so, calls a method in our controller
*  `ng-submit` like ng-click, but allows you to hit enter on a field to submit instead of just clicking a button.
*  `ng-init` to specify the initial value of say a radio button
*  `ng-checked` if you need one-way data binding for a checkbox (otherwise use ng-model)
*  `ng-switch` is a switch statement

#Unit Testing and Test Framework

__Karma__ for unit test runner and __Jasmine__ for the test framework.

##Forms

To setup a form, we can do:

    <form ng-submit="ctrl.submit()" name="myForm">
        <input type="text" ng-model="ctrl.user.username" required ng-minlength="4">
        <input type="submit" value="Submit" ng-disabled="myForm.$invalid">
    </form>

There's a lot going on, including:

*  We give the form a name `myForm`
*  We use HTML5 validator for `required`
*  We enforce length using `ng-minlength`
*  On the Submit button, we add an `ng-disabled` (which disables the element if the condition is true)

###FormController

When you use forms and give them a name, AngularJS creates a __FormController__, which has the current state of the form.  These states include:

*  `$invalid` and `$valid` when any of the validations (e.g. required, ng-minlength) are invalid or valid
*  `$error` when one of the form fields has an error
*  `$pristine` is the default state of a form; used to check if user started typing.  `$dirty` is the opposite of pristine.

You can use a few directives to help with form validation.  

*  `required` is an HTML5 validator
*  `ng-required` allows a field to be conditionally required
*  `ng-minlength` and `ng-maxlength` is field length
*  `ng-pattern` is a regex
*  `type="email"` has email validation
*  `type="number"` has a number validation (can have min, max attributes)
*  `type="date"` is a HTML datepicker
*  `type="url"` has URL validation

We can either display specific error messages and/or format with CSS (e.g. highlight field).  For our CSS, we have a CSS class that goes with each:

*  `required` with `ng-valid-required` or `ng-invalid-required`
*  `min` with `ng-valid-min` or `ng-invalid-min`
*  `email` with `ng-valid-email` or `ng-invalid-email`
*  `invalid` with `ng-invalid`
*  `valid` with `ng-valid`

###ngModel Options

With `ng-model`, we can add the `ng-model-options` directives that have the following options:

*  `updateOn` is a string that indicates which events of the input should the directive listen on and update.
*  `debounce` can be an integer or an object; it sets the time in ms AngularJS should wait before updating the model variable.
*  `allowInvalid` is false by default; can allow saving model even if fields are invalid 
*  `getterSetter` treats the `ng-model` expression as a getter/setter instead of a variable

###ngForm

A regular HTML `form` does not allow nesting.  The `ngForm` allows nesting (e.g. a subform inside a form).  

##Services

AngularJS services are functions or objects that can hold behavior or state across the application.  We only instantiate each Service once.  Services can be implemented as factory, service, or provider; they are used in repeated behaviors, shared state, caches, factories, etc.  For Services, you can use the `$rootScope` instead of `$scope`.

###Controllers versus Services

Controllers are the presentation logic while Services are the business logic.
Controllers are directly linked to a view while Services are independent of views
Controllers drives the UI while Services drives the application
Controllers are one-off, specific while Services are reusable
Controllers are responsible for decisions like what data to fetch, what data to show, how to handle user interactions, and styling and display of the UI
Services are responsible for making server calls, common validation logic, application-level stores, and reusable business logic

##Dependency Injection

AngularJS services are singleton for the scope of the application (restrict instantiation of a class to only one object)
AngularJS guarantees that the function we provide to the service declaration is only executed once lazily (the first time its called / needed); future dependents get that same instance.

###Safe style of Dependency Injection

Use this.

    myModule.controller("MainCtrl", ["$log", function("$log") {}]);

You might see this style (though not recommended due to minification losing info about $log:

    myModule.controller("MainCtrl", function($log) {});

###Order of Injection

Dependencies are injected based on the order in which they are listed.  In the below, the $log service is injected into $l and the $window service is injected into $w.

    myModule.controller("MainCtrl", ["$log", "$window", function($l, $w) {}]); 

###$

All AngularJS Services are prefixed with the __$__ sign, like $log, $http, $window.  When you create your own service, do NOT use a __$__ sign.

##AngularJS Services

There's three different ways to create and register __services__; these are by __factory__ (functional programming style, return functions and objects), __service()__ (Class/Object Oriented programming style), or by __provider__ (usually used when we setup some configuration for the service before the application loads).

###Common AngularJS Services

*  `$window` service is a wrapper around the global window object; avoids global state (esp in tests)
*  `$location` service allows interaction like get and changes to the URL in the browser
*  `$http` is used to make XHR requests like __GET__ and __POST__ requests, set the headers, deal with server responses and failures.

###Creating a Service

If we're creating a service using the __factory__ method, we setup similar to a __controller__.  We use the `angular.module().factory` function, as seen below.

    angular.module('notesApp', [])
        .controller('MainCtrl', [function() {
            ...
            }])
        .controller('AnotherCtrl', ['ItemService', function(ItemService) {
            ...
            }])
        .factory('ItemService', [function() {
            var items = [
                {id: 1, label: 'Item 0'},
                {id: 2, label: 'Item 1'}
            ];
            return {
                list: function() {
                    return items;
                },
                add: function(item) {
                    items.push(item);
                }
            };
        }]);

##Server Communication Using the $http directive

__$http__ allows you to do GET, POST requests.  In our controller, we specify a dependency on __$http__, which we use like __$http.get()__ followed by a __.then()__.  This allows chaining of requests.

###Promises

Instead of callbacks, there's __promises__.  Promises solves the issue of nesting multiple callbacks (i.e. callback hell) and not having a common error handler.  

###$q

The __$q__ service allows you to trigger the error handler for the next promise in the chain.  The __$q__ service can create a deferred object with __defer()__ 

###Config file

When we do a $http method, we can pass in a config file.  It might look like:

    {
        model: string,  // e.g. GET or POST
        url: string,
        params: object,  // e.g. [{key1: 'value1', key2: 'value2'}] would be ?key1=value1&key2=value2
        data: string or object,
        headers: object,
        ...
    }

This would be used like `$http.get(url, config)`.


###$http defaults with $httpProvider.defaults

To setup a default config, we can use __$httpProvider__ (in particular, __$httpProvider.defaults__) in the config section (`angular.module().config([])`)

###Interceptors

__Interceptors__ handle request-level actions like logging and adding in some authorization heaers to the request (i.e. authentication checks).  This is done with __$httpProvider__.

##Filters

AngularJS has filters that you can apply like `{{ ctrl.name | lowercase }}`.  The `json` is a good way to debug.  You can create custom filters with `angular.module.filter()`

##ngRoute

AngularJS routing is declarative, with all the routes in one configuration section where it specifies where the route is and what to do when the route is encoutered.  In a single page application, we do not reload the entire page again.  Instead, the shebang `#` denotes that the rest of the URL is loaded on the client side instead of the server side.  For example, www.somepage.com/first/page/ to www.somepage.com/second/page loads the entire page while www.somepage.com/#/first/page to www.somepage.com/#/second/ doesn't cause a whole page refresh.

###How routing is done

Routing is done using `ng-view` and define the routing in the __config__ section using __$routeProvider__.  We say `$routeProvider.when(some_url, some_template_or_template_url)` and `.otherwise()` as a default.

###Resolve

One of the route configuration options is __resolve__.  Resolves are a way to execute finish asynchronous tasks before a particular route is loaded.  This is a way to check if a user is logged in and has authorization and permissions.  Resolves are a set of keys and functions.  Each function can return a value or a promise.

###$routeParams

With __$routeParams__ you can pass the context (i.e. additional information) across different routes.

##Directives

Directives can be classed into __behavior modifiers__ (add or modify existing UI and HTML snippets like `ng-show`) and __reusable components__ (where the directive renders whole new HTML using some type of rendering logic and some business logic).  There's two main directives for business logic built into AngularJS: `ng-include` and `ng-switch`.

###ng-include

__ng-include__ is a directive that takes a path to an HTML file and replaces that with the HTML file.  Note that if you inline the html file, put a set of `''`.  ng-include is good for extracting small snippets of HTML, but is limited in that naming variable needs to be consistent across all controllers and HTML files.

    <div ng-include="mainCtrl.stockTemplate"></div>  // points to controller
    <div ng-include="'views/stock.html'"></div>  // inline, notice the extra ''

###ng-switch

__ng-switch__ is a directive that lets us add functionality to the UI for selectively displaying certain snippets of HTML.  Basically, it's a switch statement in HTML that shows different HTML depending on conditions (e.g. a bunch of tabs).  This is used along with adding children elements with __ng-switch-when__ and __ng-switch-default__ directives.

##Custom Directives

What we want to do with custom directives is to make our intention declarative by specifying in the HTML what something is or tries to do while keeping it reusable.  Creating a custom directive is similar to creating a Controller or Service; we define a __directive definition object__ using `angular.module.directive()`.  The limitation using this method is that we can only use this as `<div stock-widget>` and not `<stock-widget>`

    // HTML
    <div stock-widget></div>
    
    // JavaScript
    angular.module('stockMarketApp', [])
        .directive('stockWidget', [function() {
            return {
                // Directive definition goes here
                templateUrl: 'stock.html',
                restrict: 'AE',
                scope: {
                    stockData: '=',
                },
                link: function($scope, $element, $attrs) {
                    $scope.getChange = function(stock) {
                        return Math.ceil((stock.price - stock.previous) / stock.previous) * 100);
                    };
                },
                replace: false
            };
    }]);

Note on naming: AngularJS converts dashes to camelCase (e.g. `stock-widget` turns into `stockWidget`)

###Custom Directives Options: template / templateURL

*  `template` if you want to inline some HTML
*  `templateUrl` if you want to point to a HTML file (e.g. stock.html)
*  `restrict` if you want to say only use an attribute (`A`, the default, e.g. `<div stock-widget></div>`), `E` as a new HTML element (`<stock-widget></stock-widget>`), `C` for class name (`<div class="stock-widget"></div>`), `M` for comments.
*  `link` is used to do a 'link function'; it defines APIs and functions that are necessary for the directive, in addition to manipulating and working with the DOM.
*  `scope` says what variables this has access to and how they're linked
*  `replace` says if we want to replace the current directive with something else.  This is deprecated so don't use.

###Custom Directives Options: link function option

With the link option, we pass in the scope of the element the directive is working on, the HTML DOM element the directive is operating on, and all the attributes on the elements as strings.  This looks like `link: function($scope, $element, $attrs) {}`.  Example code below:

    link: function($scope, $element, $attrs) {
                    $scope.getChange = function(stock) {
                        return Math.ceil((stock.price - stock.previous) / stock.previous) * 100);
                    };
                }

The neat thing is that we pass in our scope.  If we did `<span class="stock-change" ng-bind="getChange(stock) + '%'"></span>`, instead of binding to `mainCtrl.getChange(stock)`, the `stockWidget` directive has its own scope.

##Custom Directives Options: Scope

The __$scope__ (__ViewModel__) is the glue between the __View__ and __Controller__.  Each directive inherits its parent's scope, which is passed to the `link` function.  Its considered bad practice to modify the parent scope from the child.  It ends up being an issue because adding variables/functions to the child modifies the parent, these variables might override existing variables with the same name, or if we rename properties.

The scope key can take one of three values:

*  `false` is the __default__ value, which means that the directive scope is the same as the parent scope.  Any modifications are immediately reflected in the parent as well.
*  `true` means that the directive scope inherits the parent scope, but creates a child scope of its own.  This means that we have access to the parent functions and variables, but any changes are not updated to the parent and are only local to the specific directive.  This is the __recommended__ access if you need parent variables.
*  `object` means we pass an object with keys and values to the scope, creating an __isolated scope__.  This scope does not inherit anything from the parent.  This is the best option when creating reusable components, which are indepdendent of how and when the components are used.  If we pick object, we can specify the three types of values that can be passed in.  These are `=` (value treated as a JSON object), `@` (value treated as a string; can be used with AngularJS binding expression `{{ }}`), or `&` (value is a function in some controller)

##AngularJS Life Cycle

1. The HTML page is loaded
    *  The HTML page loads the AngularJS source code
    *  The HTML page loads the application's JavaScript code
2. The HTML page finishes loading
3. The document `ready` event is fired; AngularJS bootstraps and searches for any and all `ng-app` attribute in the HTML
4. Within each `ng-app`, the __compile__ step occurs:
    *  The compile step goes through each line of HTML and looks for AngularJS directives
    *  For each directive, it executes the necessary code defined by the directive's definition.  This might load or create new HTML
    *  At the end of the compile step, a __link function__ is generated for each directive that has a handle on all the elements and attributes that need to be controlled by AngularJS
5. AngularJS takes the __link function__ and combines it with the __scope__.  The scope has the variables and contents that need to be displayed in the UI.  This generates the __live view__ that the user sees and interacts with.
    *  Each controller and subcontroller is instantiated with its own scope that will be used to display data in the UI
    *  AngularJS adds watchers and listeners for all the directives and bindings to ensure it knows which fields it needs to track and which data field to bind
6. We have a live, interactive view with the content filled in for the user

###The Digest Cycle (Updates to UI)

AngularJS updates the UI in response to one of the following events:
    *  The user makes a modification (types in, check checkbox, click button)
    *  A server request is made and its response comes back
    *  A $timeout or $interval is used to execute something asynchronously

##Third Party Libraries

###Bower

Manage, update, version your third party packages using __Bower__, a package management tool.  Install with `npm install bower`, then list your dependencies usually in a file `bower.json`.  To install, do `bower install` or `bower update`.  You won't need to put third party libraries into your repository and others can see what libraries and versions you're using.  When you install with bower, you can save additional information using `$bower install --save` option.


###Grunt

You can install Grunt CLI, the Command-line interface for Grunt.  This will allow you to do the following commands:
*  When you install new bower components, you can then just use `grunt serve` and it'll automatically include those javascript files into your index.html file.
*  With `grunt build`, the `/app` folder is compiled into a distributable package for production use.
*  You can tests with `grunt test` by going through the Karma test runner.

###Yeoman

Yeoman is a workflow management tool.

*  Install with `npm install -g yo`
*  Install generator with: `npm install -g <some_generator>`


##Questions

[check] * How to structure?  Use yeoman to get setup; install with 'grunt',
* For an AngularJS app, how much of it is AngularJS and how much Django?  (e.g. Django REST framework, rest all handled by AngularJS?)  Just use Django REST Framework only.
* For Services, is it more common to use factory or service (or provider)?
    - factory is de facto; can use service too
* Example Project where we use Django REST Framework and AngularJS?
    - e.g. CitizenVC (but closed), open source for Django REST Authentication, corresponding AngularJS repo.  Run those in Parallel.  Also has KitchenSink UI.
* Do we use optional modules?  e.g. ngResource for APIs, ngRoute for routing (or ui-router for routing)
    - Deferred to the Angular Generator (Yeoman and Bower).  Generator creates a list of packages, Node can bundle up all those assets.
    - Somethings are built at build-time (e.g. ng-constant)
    - JavaScript
    - LESS or SASS; we mainly use LESS
    - Charts: Highcharts is the default, otherwise go D3 (D3 go to Tabatha)

Peter created ng-django, passed in context; https://github.com/Tivix/django-base-project/tree/angularjs-components/django_base_project/django_base_project/apps/ngDjango
Use Angular tells element how to behave, Django serves up the template

Example Projects with AngularJS:

https://www.madewithangular.com/#/
