---
layout: post
title: Django REST Framework (DRF)
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
    -  [REST](#rest)
    -  [Django REST Framework (DRF)](#drf)
*  [Django Models](#djangomodels)

*  [DRF Relationships](#drfrelationships)
    -  [String Related Field](#stringrelatedfield)
    -  [Primary Key Related Field](#primarykeyrelatedfield)
    -  [Hyperlinked Related Field](#hyperlinkrelatedfield)
    -  [Slug Related Field](#slugrelatedfield)
    -  [Hyperlinked Identity Field](#hyperlinkedidentityfield)
    -  [Nested Relationships](#nestedrelationships)

##<a id="summary">Summary</a>

####<a id="rest">REST</a>

__Representational State Transfer (REST)__ is a software architecture style that includes best practices for scaling web services.  The idea is that client-server is separated for portability of code and ability to scale.  REST systems communicate over Hypertext Transfer Protocol and uses commands like (GET, POST) to retrieve and send data.  REST basically exposes application data through an API and with django, it's easy to use django-rest-framework to expose our models and views.

####<a id="drf">Django REST Framework (DRF)</a>

__Django REST Framework (DRF)__ is a quick and standard way of building REST APIs.  It has a browsable GUI interface, many built-in forms of authentication, and supports many types of serialization.  You don't technically need to use this to make an API (instead send payloads with requests and responses), but DRF is recommended since it is standardized.

##<a id="drfrelationships">DRF Serializer Relationships</a>

While creating your serializers, you'll want to know their relationships (just like in Django how you define the model relationships).  You can always check your current relationships in Django and DRF by using `python manage.py shell` to import, instantiate, and `print repr(myserializer)` or `print repr(mymodel)`.  Here we'll see what the possible serializer relationships are:

####<a id="stringrelatedfield">String Related Field</a>

`StringRelatedField` represents the target of the relationship using its `__unicode__` method in the Django Model.  Pretty straightforward; if my model has a __unicode__ of 'Stuff', the serializer output shows 'Stuff'.

####<a id="primarykeyrelatedfield">Primary Key Related Field</a>

`PrimaryKeyRelatedField` represents the target of the relationship usings its primary key.  So say we have a Django Model with a primary key (usually its the `id` field) with a value of 10.  Our serializer now shows the field as 10.

####<a id="hyperlinkrelatedfield">Hyperlinked Related Field</a>

`HyperlinkedRelatedField` represents the target of a relationship using a hyperlink.  This field is just a hyperlink of where the object is, e.g. `http://williamqliu.com/stuff/10`.

####<a id="slugrelatedfield">Slug Related Field</a>

`SlugRelatedField` represents the target of a relationship using a field on the target.  So this one is basically creating a new field in your serializer that is an existing field in another model.  Default is Read and Write (though you can change flag to `read_only` if you want).

####<a id="hyperlinkedidentityfield">Hyperlinked Identity Field</a>

`HyperlinkedIdentityField` represents the target of a relationship using a field on a hyperlink's identity.  This field is applied to an identity relationship (e.g. the url) or added as an attribute to your serializer.  Always Read Only.

####<a id="nestedrelationships">Nested Relationships</a>

You can nest relationships by using serializers as fields.  By default, these are read only.  If you want to be able to write to them, then you need to create either or both `create()` and/or `update` methods to explicitly specify how the child relationships should be saved.


