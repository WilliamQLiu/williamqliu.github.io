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
*  [](#)


- - - -

##<a id="summary">Summary</a>

AngularJS is a client side web framework in JavaScript.

####<a id="whyangular">Why Angular</a>

AngularJS is a JavaScript framework instead of a library (think lots of structure).  Angular provides a higher level abstraction with a particular focus on CRUD (as opposed to making something like Games or GUI editors).

*  A __library__ is a collection of functions that you can call whenever you see fit (e.g. jQuery, backbone)
*  A __framework__ is a particular implementation of a web application with built-in data binding, decouples DOM manipulation to improve testability, opinionated directory layout

####<a id="stepthrough">Step through of an App</a>

What happens when an Angular app loads?

On our browser, when we visit a web site, say we load up a html page (aka __template__) that has some angular markup.  What angular does in the background is it quickly parses the angular markup from the template using the __compiler__.  The compiler basically teaches the browser new behaviors.  With the new features, the DOM is created and we call this a __view__.

So how is this different?  In a standard multi-page web site and some single page applications, the server creates the HTML and joins the data on the server, then sends this finished response to the client browser.  With Angular, the _template_ and the _data_ is sent to the browser and it gets assembled there using the compiler.  Neat huh?  The processing isn't done mostly on the server anymore, but instead on the client's browser.

####<a id="structure">High Level Structure of an AngularJS App</a>

So what's the pieces of an Angular app?  Angularjs follows the Model-View-Controller Pattern to structure a website; this structure decouples the code and separates concerns.  A user interacts with the Controller, which then manipulates the Model, which then updates the View that is presented to the user.  The pieces are:

*  The __Model__ is the representation of the data.  This is instantiated from the Controller.  When data binding occurs, the data is synced between the Model and the View.
*  The __View__ is the projection of the model through the HTML template (e.g. app/index.html).  When the Model is updated, the View is updated.  Views are made up of:
  *  __Directives__ extend regular HTML with custom functionality.  Most of the time these are used to create UI components.  There are many different directives including `ng-repeat`, `ng-controller`, `ng-bind`, `ng-app`, `ng-change`, `ng-model`, `ng-submit`, `ng-click`.  You see these in the html, like: `<html ng-app>`, which represents an Angular directive named 'ng-app'.
  *  __Expressions__ are variables placed inside data bindings (`{{}}`).  E.g. `{{ phone.name }}`.  Note: AngularJS is slightly different than JavaScript expressions.
*  The __Controller__ is the business logic behind the views.  The Controller's job is to establish the data binding between the Model and the View by exposing variables and functions to expressions and directives.  The Controller uses the `ng-controller` directive.  It's just a constructor function that takes in a `$` parameter, mainly the `$scope` parameter.  There are additional parameters including $https, $locationUrl, etc.

####<a id="comparisons">Comparisons to other JS Libraries and Frameworks</a>

*  There is a high level of abstraction; we don't have to pepper the HTML with classes or ids to identify where to attach event listeners.
*  We don't have to reigster any event listeners or write callbacks

- - - -

##Module

We call AngularJS to create an application name (e.g. store) and then we place our dependencies in the [].

####app.js

    var app = angular.module('store', [ ]);

We then call this in our html under:

####index.html

    <html ng-app="store">
    <body>
      <script type="text/javascript" src="app.js"></script>


- - - -

##Data Binding

