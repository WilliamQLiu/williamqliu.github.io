---
layout: post
title: AngularJS
---

## {{ page.title }}

- - - -

##Summary

Angularjs follows the Model-View-Controller Pattern to structure a website; this structure decouples the code and separates concerns.  A user interacts with the Controller, which then manipulates the Model, which then updates the View that is presented to the user.  The basic idea is that:

*  The __Model__ is the representation of the data.  This is instantiated from the Controller.
*  The __View__ is the projection of the model through the HTML template (e.g. app/index.html).  When the model is updated, the View is updated.
  *  __Directives__ create standalone UI components.  There are many different directives including 'ng-repeat', 'ng-controller', 'ng-bind', 'ng-app', 'ng-change', 'ng-model', 'ng-submit', 'ng-click'.  You see these in the html, like: `<html ng-app>`, which represents an Angular directive named 'ng-app'.
  *  __Expressions__ are the data bindings.  E.g. `{{ phone.name }}`
*  The __Controller__ is just a constructor function that takes in a `$` parameter, mainly the '$scope' parameter.  The Controller's job is to establish the data binding between the Model and the View.  There are additional parameters including $https, $locationUrl, etc.
The Controller uses the 'ng-controller' directive.

    <body ng-controller="PhoneListCtrl">
          ...
    </body>

Controllers take in a $scope parameter, which is the glue that binds the View, Model, and Controller to work together.  With this, you can call additional functions like $watch, $https, $locationURL.


Module

We call AngularJS to create an application name (e.g. store) and then we place our dependencies in the [].

app.js

    var app = angular.module('store', [ ]);

We then call this in our html under:

index.html

    <html ng-app="store">
    <body>
      <script type="text/javascript" src="app.js"></script>