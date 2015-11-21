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
*  [DRF Serializers](#drfserializers)
    -  [DRF Serializer Relationships](#drfrelationships)
    -  [String Related Field](#stringrelatedfield)
    -  [Primary Key Related Field](#primarykeyrelatedfield)
    -  [Hyperlinked Related Field](#hyperlinkrelatedfield)
    -  [Slug Related Field](#slugrelatedfield)
    -  [Hyperlinked Identity Field](#hyperlinkedidentityfield)
    -  [Nested Relationships](#nestedrelationships)
*  [DRF Views](#drfviews)
    -  [GenericAPIView](#drfgenericapiview)
    -  [GenericAPIView Mixins](#drfgenericapiviewmixins)
    -  [Concrete Views](#drfconcreteviews)
    -  [APIView](#drfapiview)
    -  [GenericAPIView and APIView Routing](#drfapiviewrouting)
    -  [ViewSets](#drfviewsets)
    -  [Routers](#drfrouters)
*  [DRF Authentication](#drfauthentication)
    -  [Create Token](#drfcreatetoken)


##<a id="summary">Summary</a>

####<a id="rest">REST</a>

__Representational State Transfer (REST)__ is a software architecture style that includes best practices for scaling web services.  The idea is that client-server is separated for portability of code and ability to scale.  REST systems communicate over Hypertext Transfer Protocol and uses commands like (GET, POST) to retrieve and send data.  REST basically exposes application data through an API and with django, it's easy to use django-rest-framework to expose our models and views.

####<a id="drf">Django REST Framework (DRF)</a>

__Django REST Framework (DRF)__ is a quick and standard way of building REST APIs.  It has a browsable GUI interface, many built-in forms of authentication, and supports many types of serialization.  You don't technically need to use this to make an API (instead send payloads with requests and responses), but DRF is recommended since it is standardized.

##<a id="drfserializers">DRF Serializers</a>

In Django, you have data in Model objects/instances and querysets.  __Serializers__ allow you to convert from these objects and querysets into native Python that can then be rendered into web friendly formats like JSON.

####<a id="drfrelationships">DRF Serializer Relationships</a>

While creating your serializers, you'll want to know their relationships (just like in Django how you define the model relationships).  You can always check your current relationships in Django and DRF by using `python manage.py shell` to import, instantiate, and `print repr(myserializer)` or `print repr(mymodel)`.  For example:

    >>> from myapp.serializers import StuffSerializer
    >>> serializer = StuffSerializer()
    >>> print repr(serializer)


Here we'll see what the possible serializer relationships are:

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

##<a id="drfviews">DRF Views</a>

So there's a few different ways that DRF gives you to create views.  Starting from most generic to more specific, we have the following:

*  A __GenericAPIView__ is a pre-built skeleton view that has the ability to extend functionality through the use of other pre-built views (like ListAPIView, CreateAPIView, DestroyAPIView) or add specific functionality with ModelMixins (like CreateModelMixin, UpdateModelMixin, etc.)  These generic pre-built views define actions (e.g. `.list()`, `.create()` instead of HTTP methods (e.g. `.get()` or `.post()`).  The idea is that you can mix and match Mixins to your liking or use one of those pre-built views.  For example, a `CreateAPIView` is only a `GenericAPIView` + `CreateModelMixin`.  In my opinion, using something like `CreateAPIView` is a little confusing; either use __GenericAPIView__ or use __APIView__.
*  Class based View with __APIView__ - this is similar to a regular Django View class where the incoming request is dispatched to an appropriate HTTP method (like `.get()` or `.post()`).  You return a rest_framework `Response()`.  This is more detailed / specific than a __GenericAPIView__.
*  Function based View by adding the __@api_view()__ decorator in a function based view.  This is similar to a regular Django View where you check if `request.method == 'POST'`, etc.

####<a id="drfgenericapiview">DRF APIGenericView (i.e. a Base View Class)</a>

With a __GenericAPIView__, you have a skeleton API View that has things every View would need, like what the __queryset__ is, the __serializer_class__, etc.  From this basic skeleton, you can add in some built-in functionality for the most common operations with Mixins (e.g. `CreateModelMixin`).  When you combine the GenericAPIView with Mixins, you get some generic views (e.g. `DestroyAPIView` = `GenericAPIView` + `DestroyModelMixin`.  You basically set some class attributes and override the view functions as needed.  Since this is a Generic API View, we define the __http methods__ (e.g. GET, POST, PATCH) and then we call the Mixin's __actions__ (e.g. list, create, retrieve, update).


####<a id="drfgenericapiviewmixins">DRF Generic View - Using Mixins</a>

__Mixins__ are reusable components for class based views.  We add Mixins to a GenericAPIView class so that the class can gain additional functionality, in this case it provides an action that has `.something()` functionality where something is say `.list()`, `.create()`, `.retrieve()`, `.update()`, `.destroy()`.  Note that these actions are abstractions of __http methods__ (like GET, POST).  That means we define say our GET, then when we return we use the `.list()` functionality.  In this example, the `ListModelMixin` gives the `.list()` functionality and the `CreateModelMixin` gives the `.create()` functionality to our __GenericAPIView__.

    from rest_framework import mixins, generics
    
    from .models import Post
    from .serializers import PostSerializer
    
    
    class PostListCreate(mixins.ListModelMixin, mixins.CreateModelMixin, generics.GenericAPIView):
        queryset = Post.objects.all()
        serializer_class = PostSerializer
    
        def get(self, request, *args, **kwargs):
            return self.list(request, *args, **kwargs)
    
        def post(self, request, *args, **kwargs):
            return self.create(request, *args, **kwargs)

This is the same as the Concrete View below (though I prefer to stay away from the Concrete View):

    class PostListCreate(generics.ListCreateAPIView):
        queryset = Post.objects.all()
        serializer_class = PostSerializer
    
        def list(self, request):  # define to override list
            queryset = self.get_queryset()
            serializer = PostSerializer(queryset)
            return Response(serializer.data)

####<a id="drfconcreteviews">DRF Concrete Views</a>  

I prefer not to use a Concrete View like `ListCreateAPIView` or `UpdateAPIView`.  The reasoning is that we abstract away from __http_methods__ (e.g. GET, POST) and instead only define the actions (e.g. list, retrieve).  To me, it's difficult to go from the action (e.g. list) and tie that to the http method (e.g. GET)

    from django.contrib.auth.models import User
    from rest_framework import generics
    from rest_framework.permissions import IsAdminUser
    
    from myapp.serializers import UserSerializer
    
    class UserList(generics.ListCreateAPIView):
        queryset = User.objects.all()
        serializer_class = UserSerializer
        permission_classes = (IsAdminUser,)
        paginate_by = 100
    
        def get_queryset(self):
            """ Override default queryset to only get user's account """
            user = self.request.user
            return user.accounts.all()
     
        def list(self, request):
            queryset = self.get_queryset()
            serializer = UserSerializer(queryset, many=True)
            return Response(serializer.data)


####<a id="drfapiview">DRF APIView</a>

The class based __APIView__ is a great balance between boilerplate and custom code.

    from rest_framework.views import APIView
    from rest_framework.response import Response
    
    from myapp.serializers import PostSerializer
    from myapp.models import Post
    
    
    class ListPost(APIView):
    
        queryset = Post.objects.all()
        serializer_class = PostSerializer
    
        def get(self, request, format=None):
            queryset = Post.objects.all()
            serializer = PostSerializer(queryset, many=True)
            return Response(serializer.data, status=status.HTTP_200_OK)

####<a id="drfapiviewrouting">DRF GenericAPIView and APIView Routing</a>

In your main urls.py file, link to your existing app api urls.py.

    #urls.py
    urlpatterns = patterns('',
        (r'^api/', include(patterns('',
            url(r'^blog/', include('blog.api.urls')),
            url(r'^accounts/', include('accounts.api.urls')),
    ), namespace='api')),
    ...

####<a id="drfviewsets">DRF ViewSets</a>

DRF allows you to combine the logic for a set of related views into a single class called a __ViewSet__ (aka 'Resources', 'Controllers').  A ViewSet is simply a Class Based View that does not provide any method handlers (like `.get()`, `.post()`) and instead provides actions (like `.list()`, `.create()`).  You have your standard __ViewSet__ (inherits from __APIView__), __GenericViewSet__ (inherits from __GenericAPIView__), and __ModelViewSet__ (inherits from __GenericAPIView__).

    from django.contrib.auth.models import User
    from django.shortcut import get_object_or_404
    from myapp.serializers import UserSerializer
    from rest_framework import viewsets
    from rest_framework.response import Response
    
    
    class UserViewSet(viewsets.ViewSet):
        
        def list(self, request):
            queryset = User.objects.all()
            serializer = UserSerializer(queryset, many=True)
            return Response(serializer.data)
    
        def retrieve(self, request, pk=None):
            queryset = User.objects.all()
            user = get_object_or_404(queryset, pk=pk)
            serializer = UserSerializer(user)
            return Response(serializer.data)

Personally I think this has too much magic and you should avoid viewsets; instead opt for APIGenericView with Mixins or APIView.

####<a id="drfrouters">DRF Routers</a>

You can wire up your urls manually or if you used a __ViewSet__, you can then use DRF's __routers__ to automatically do URL routing for you.  There's a couple of default routers including __SimpleRouter__ and __DefaultRouter__.  In my opinion, don't use ViewSets because of the additional magic (so thus you can't use these router functions), but the patterns here are good to know and follow since they offer a standardized approach to API design.

    from rest_framework import routers
    
    router = routers.SimpleRouter()
    
    router.register(r'users', UserViewSet)
    urlpatterns += router.urls

We pass in a __prefix__ (in this case `users`) and the ViewSet.  For example, the pattern for the above __SimpleRouter__ is this:

*  `^users/$` Name: `user-list`  # from UserViewSet.list
*  `^users/{pk}/$` Name: `user-detail`  # from UserViewSet.retrieve

With __DefaultRouter__ we have an API root view and allows for an optional style format suffix (e.g. `.json`)

See the DRF documentation [here](http://www.django-rest-framework.org/api-guide/routers/) for details

##<a id="drfauthentication">DRF Authentication</a>

You can use DRF to authenticate that you are you.

####<a id="drfcreatetoken">Create Token</a>

You can create a token by going into the dbshell.

    will = User.objects.create(username='will')
    new_token = Token.objects.create(user=will)
    print new_token
    <Token: fjsdaklfjdsalfdsjaflasjflsa>

Then in Postman, insert in 'Headers' the following key and value:

    Authorization   Token fjsdaklfjdsalfdsjaflasjflsa
