---
layout: post
title: Python Basics
---

## {{ page.title }}

- - - -

**About Python**

Python is a general purpose programming language that focuses on code readability.  This is a quick summary of what you will need to know about Python data types and control flow.

*  [Numbers (int, float)](#numbers)
*  [Sequence Types (i.e. can slice)](#sequencetypes)
    -  [Strings (str) and Unicode (unicode)](#strings)
    -  [Lists (list, stacks, queues, list comprehension)](#lists)
    -  [Tuples](#tuples)
    -  [Sequence Methods](#sequencemethods)
    -  [Sorting Sequences](#sequencesort)
*  [Hash Table (i.e. key-value pair)](#hashtables)
    -  [Dict](#dict)
    -  [Set](#set)
*  [Control Flow](#controlflow)
    -  [if else statement](#ifelse)
    -  [for statement](#for)
    -  [range(), xrange()](#range)
    -  [lambda expression](#lambda)
*  [Debugging Functions](#debug)
    -  [help()](#help)
    -  [dir()](#dir)
    -  [globals()](#globals)
    -  [type()](#type)
    -  [id()](#id)

- - - -

## <a id="numbers">NUMBERS</a>

Python can do simple calculations like addition, subtraction, division, multiplcation, and calculate powers.  

     2 + 2**3  # Addition with 2 to the power of 2
     #10

####Floor Division

Integers (e.g. `2`, `4`, `20`) have type **int** and the fractional parts (e.g. `5.0`, `1.6`) have type **float**.  When doing division with a numerator and denominator of type int, then there is an automatic **floor division** (i.e. rounds down to the nearest integer, e.g. `11/4 = 2` even though result should be `2.75`).  To avoid floor division, make one of the operands a float (e.g. `11/4.0` and your result will be `2.75`).  You can also force a floor division (regardless of data types) using `//` operator

     (50 - 5 * 6) / 3  # Floor division because operands are both type int
     #6
     
     (50 - 5 * 6) / 3.0  # No floor division, at least one operand type float
     #6.666666666666667
     
     #(50 - 5 * 6) // 3  # Forces floor division
     #6

- - - -

##<a id="sequencetypes">SEQUENCE TYPES</a>

###<a id="strings">Strings</a>

Python can also manipulate strings using single quotes (`'...'`) and double quotes (`"..."`) with `\` as an escape character.  Strings are **immutable** (i.e. object can't be altered, a new object has to be created if a different value has to be stored)

    temp = 'hello world'  # single quotes
    print temp  # hello world

    temp = "hello world"  # double quotes
    print temp  # hello world
    
    temp = 'doesn\'t'  # use \ to escape a quote
    print temp  # doesn't
    print type(temp)  # <type 'str>'

####Raw Strings

If you don\'t want characters prefaced by `\` to be interpreted as a special character (e.g. `\n` used in next line), then use `r` before the string

    print "C:\some\name"  # here \n means newline
    #C:\some
    #ame
    
    print r"C:\some\name"  # here it reads the raw string
    #C:\some\name

####Joining and Slicing Strings

If you want to join strings together use `+`.  If you want to slice strings, use `[]`.

    word = "Python"
    print word[1]  # Get character in position 0
    #y
    
    print word[-1]  # get character in last position
    #n
    
    print word[2:4]  # Slice characters
    #th

####String Methods

*  `str.count()` returns the number of occurrences of substring
*  `str.decode()` returns a decoded version of the string
*  `str.encode()` returns an encoded version of the string
*  `str.endswith()` returns `True` if suffix found, otherwise `False`
*  `str.find()` returns `True` if substring found, otherwise `False`
*  `str.format()` returns a formatted string
*  `str.join()` returns a concatenated string on the iterable
*  `str.lower()` returns a lower case conversion of the string
*  `str.replace(old, new)` returns a new string with old substring replaced with new
*  `str.startswith()` returns `True` if suffix found, otherwise `False`

- - - -

###<a id="lists">Lists</a>

Lists are a way to hold items in order.  The items can be different data types or the same type (if it's the same type and you need speed, look up arrays, in particular numpy arrays).  Lists can also be sliced and concatenated (i.e. sequence type).  Lists are also **mutable** (i.e. there are methods to modify the object)

    squares = [1, 4, 9, 16, 25]  # make a list of numbers
    print squares
    #[1, 4, 9, 16, 25]
    
    squares + [36, 49, 64, 'etc']  # Add in another list
    #[1, 4, 9, 16, 25, 36, 49, 64, 'etc']
    
    print squares[2:4]  
    #[9, 16]
    print type(squares)  # <type 'list'>

####List Methods

*  `list.append(x)` adds an item `x` to the end of the list
*  `list.extend(L)` extends the list by specific items
*  `list.insert(i, x)` inserts an item `x` at a given position `i`
*  `list.remove(x)` removes the first occurrence of the value `x`
*  `list.clear()` removes all items from the list
*  `list.index(x)` returns index in the list whose value is `x` or an error if none found
*  `list.count(x)` returns the number of times `x` appears in the list
*  `list.sort()` sorts the items of the list in place
*  `list.pop([i])` removes the `i` item or last item if not specified

####Stacks
A **stack** is using a list with first-in, last-out functionality.  The main methods for stacks are `append()` and `pop()`.  Note that with append, the items you add can be other lists (i.e. you can have a list with lists inside as individual items)

####Queues
A **queue** is a specific list built for efficient first-in, first-out functionality.  For more specialized containers, look up `collections`.

    from collections import deque
    queue = deque(["Eric", "John", "Michael"])
    queue.append("Terry")  # Terry arrives
    queue.append("Graham")  # Graham arrives
    queue.popleft()  # Eric (first in queue) now leaves
    queue.popleft()  # John (second in queue) now leaves
    queue  # deque(['Michael', 'Terry', 'Graham'])
    print type(queue)  # <type 'collections.deque'>

####List comprehensions

List comprehensions are a concise way of making lists based on an operation or if the item satisifes a certain condition.  This is a compact for-loop that builds lists.  Here are different ways of doing a regular loop along with the list comprehension equivalent:

    sqaures = []
    for x in range(10):  # range(10) loops from 0 - 9
        squares.append(x**2)
    print squares
    #[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
    
    squares = map(lambda x: x**2, range(10))  # same, using map
    squares = [x**2 for x in range(10)]  # same, using list comprehension

####So what makes up a list comprehension?

The basic structure is:

    [<output value> for <element> in <list> <optional criteria>]

*  We have the list comprehension enclosed with brackets `[]` because we return a list
*  In the middle section, we have `for <element> in <list>` and it looks like a regular `for-loop`.  This tells us we are iterating over the list going through each element in the sequence.
*  The `<output value>` is what we append to the list we are building.  We can say this is the element itself, the element squared, the absolute value of the element, etc.
*  The `<optional criteria>` can be a way to filter out items if they do not meet a condition.  

Examples:

    vec = [-4, -2, 0, 2, 4]
    
    [x for x in vec]  # print out the list as is
    #[-4, -2, 0, 2, 4]
    
    [x*2 for x in vec]  # create a new list with the output values doubled
    #[-8, -4, 0, 4, 8]
    
    [abs(x) for x in vec]  # apply a function to all the elements
    #[8, 4, 0, 4, 8]
    
    [x for x in vec if x >= 0]  # filter the list to exclude negative numbers
    #[0, 2, 4]    

- - - -

###<a id="tuples">Tuples</a>

**Tuples** are like read-only lists because they are immutable (instead of mutable) and are enclosed with parentheses (instead of brackets).  Tuples do care about order.

    temp = ('abcd', 786, 2.23, 'john', 70.2 )
    print temp  # ('abcd', 785, 2.23, 'john', 70.2)
    print type(temp)  #<type 'tuple'>
    
    print temp[0]  # prints first element of the tuple
    # abcd
    
    print temp[1:3]  # prints elements after 1st to 3rd
    # (785, 2.23)

####<a id="sequencemethods">Sequence Methods</a>
Assuming `s` and `t` are sequences and `n`, `i`, `j` are integers.

*  `x in s`  # True if an item of s is equal to x, else False
*  `x not in s`  # False if an item of s is equal to x, else True
*  `s + t`  # Concatenate s and t
*  `s * n`  # Create n copies of s
*  `s[i]`  # ith item of x (Note: origin is item 0)
*  `s[i:j]`  # slice of s from i to j
*  `s[i:j:k]`  # slice of s from i to j with step k
*  `len(s)`  # length of s
*  `min(s)`  # smallest item of s
*  `max(s)`  # largest item of s
*  `s.index(x)`  # index of the first occurence of x in s
*  `s.count(x)`  # total number of occurences of x in s

####<a id="sequencesort">Sorting Sequences</a>

Python has a `sort` method that modifies a list in-place and a `sorted` method that builds a new sorted list from an iterable.  NumPy also has a `sort` method, but it does not modify the list in-place.

    my_list = ['hello', 'world', 'will', 'was', 'here']
    
    #NumPy's sort; not inplace
    np.sort(my_list)
    #array(['hello', 'here', 'was', 'will', 'world'],
    #  dtype='|S5')
    my_list
    #['hello', 'world', 'will', 'was', 'here']
    
    #Python's sort; inplace, only works for lists
    my_list.sort()
    #['hello', 'here', 'was', 'will', 'world']
    
    #Python's sorted
    sorted([5, 2, 3, 1, 4], reverse=False)
    [1, 2, 3, 4, 5]

####keys

There is a `key` parameter in Python\'s `sort` and `sorted` methods that allows you to specify a function to be called on each list element prior to making comparisons.  Note this is not available in the Numpy version of `sort`.

    sorted("This is a test string from Will".split(), key=str.lower)
    #['a', 'from', 'is', 'string', 'test', 'This', 'Will']

A common pattern is to sort objects using the object indices as a key.  For example, we are interested in the sorting by the age in this tuple.

    student_tuple = [
        ('john', 'A', 15),
        ('jane', 'B', 12),
        ('dave', 'B', 10)]
    #[('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
    sorted(student_tuple, key=lambda student: student[2])  # sort by age
    #[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]

####operator (itemgetter, attrgetter, methodcaller)

Python has a few built in functions to help you get get items with `itemgetter` and `attrgetter` to get attributes

    from operator import itemgetter, attrgetter

####itemgetter

Get an item from an object using **itemgetter**.  This basically looks up an index on a sequence, then returns that value.

    #String
    itemgetter(1)('ABCDEFG')  #'B'
    
    #List
    itemgetter(2)(['Hello', 'World', 'Will'])  #'World'
    
    #Dict
    itemgetter('Age')(phonebook1)
    
    #Tuple
    itemgetter(2)(student_tuple)  # ('dave', 'B', 10)

####attrgetter

Get attributes from an object using **attrgetter**.  This basically looks up an attribute on an object.

    import operator
    class Student(object):
        def __init__(self, id, name, grades):
            self.id = id
            self.name = name
            self.grades = grades
        
        def __str__(self):
            return '%s has grade %s' %(self.name, self.grades)
    
    students = [Student(0, 'Will', 70), Student(1, 'Laura', 90), Student(2, 'Wayne', 87)]
    best_student = max(students, key=operator.attrgetter('grades'))
    print best_student
    #Laura has grade 90

- - - -

##<a id="hashtables">HASH TABLES</a>
A hash table (aka hash map) is a way to map keys to values (i.e. a key-value pair).  A hash function computes an index into an array of buckets or slots, from which the correct value can be found.

###<a id="dict">Dictionary (dict)</a>
A **dictionary** is a key-value pair.  The key can be almost any type, but usually are numbers or strings.  Value can be any Python object.  Dictionaries are enclosed with `{}` and accessed with brackets `[]`.  The idea is also called **associative arrays**, **map**, **symbol table**.  The ideas behind operations are:

*  create a dictionary
*  add (aka insert) a key-value pair
*  reassign a value
*  remove (aka delete) a key/value pair
*  clear to remove all elements
*  give key, get value
*  iterate through the dictionary

Example:

    phonebook = {}  # Create dict with {}
    phonebook = {'Name': 'Will', 'Age': 30, 'Number': 1234567890}
    phonebook['Notes'] = 'Do not give cookies'  # Add key-value pair
    phonebook['Number'] = 938477566  # Update value with key
    del phonebook['Notes']  # Delete key-value pair
    phonebook.keys()  #get keys  #['Age', 'Name', 'Number']
    phonebook.values()  # get values  # [30, 'Will', 123456789]
    phonebook.clear()  # clears all values, keeps keys
    
    #Iterate through key, values    
    for my_key, my_value in phonebook.iteritems():
        print "Key is: ", my_key
        print "Value is: ", my_value
    #Key is:  Age
    #Value is:  30
    #Key is:  Name
    #Value is:  Will
    #Key is:  Number
    #Value is:  938477566
    

- - - -

###<a id="set">Set</a>

A **set** is an unordered collection of distinct hashable elements.  Note that items have to be hashable and can't hold duplicates.  Sets have all these restrictions, but are really fast.  They're also good for set methods.

    from sets import Set
    engineers = Set(['John', 'Jane', 'Jack'])
    print engineers  # Set(['Jane', 'John', 'Jack'])
    print type(engineers)  # <class 'sets.Set'>

    # Set operations
    s1 = set(['Beta', 'Gamma', 'Alpha', 'Delta', 'Gamma', 'Beta'])
    s2 = set(['Beta', 'Alpha', 'Epsilon', 'Omega'])
    s1.union(s2)  # set(['Epsilon', 'Beta', 'Delta', 'Alpha', 'Omega', 'Gamma'])
    s1 | s2  # set(['Epsilon', 'Beta', 'Delta', 'Alpha', 'Omega', 'Gamma'])
    s1.intersection(s2)  # set(['Alpha', 'Beta'])
    s1.difference(s2)  # set(['Gamma', 'Delta'])
    s1.symmetric_difference(s2)  # set(['Epsilon', 'Delta', 'Omega', 'Gamma'])

- - - -

##<a id="controlflow">Control Flow</a>

Python has the usual control flow statements.  These include:

*  `if` statement
*  `for` statement
*  `range()` function iterates over a sequence of numbers
*  `xrange()` function is like `range()`, but doesn't store in memory
*  `lambda` expression is a small anonymous function, which means lambda: a if some_condition() else b
*  `map()` function applys a function to every item of iterable and returns a list

###<a id="ifelse">if, elif, else statement</a>

    # if statement
    if x < 0:
        print "x is negative"
    elif x==0:
        print "x is zero"
    else:
        print "x is positive"

###<a id="for">for statement</a>

    # for statement
    words = ['cat', 'window', 'defense']
    for w in words:
       print w,  # cat window defense

###<a id="range">range(), xrange()</a>

    # range() function
    for i in range(5):
        print i,  # 0 1 2 3 4
    
    # normal call with separate arguments
    temp = list(range(3, 6))
    print temp  # [3, 4, 5]
    
    $ xrange() function
    for i in xrange(2, 10):
        print i,  # 2 3 4 5 6 7 8 9

###<a id="lambda">lambda expression</a>

Lambda is a way to create small anonymous functions (i.e. a function without a name).  These are often used in combination with `filter()`, `map()`, and `reduce()`

    # a general lambda expression
    lambda x: 'big' if x > 100 else 'small'
    lambda x: x**2

####<a id="filter">filter()</a>

`filter(function, sequence)` is a function that filters out all the elements of a sequence where the function returns `True`.  The first argument is the function that returns a Boolean and this is applied to every element of the list.  A sequence is returned where the values are only where the function returns `True`.

    # filter() with number
    def lessThanFive(element):
        return element < 5  # True if less than 5, otherwise False
    fib = [0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
    result = filter(lessThanFive, fib)
    print result # [0, 1, 1, 2, 3]
    
    # filter() with strings
    names = ['Jack', 'Jill', 'Steve', '']
    filter(None, names)  # empty strings, 0's and None's are False
    
    # filter() with lambda
    result = filter(lambda x: x %2, fib)  # odd only
    print result # [1, 1, 3, 5, 13, 21, 55]
    
    # filter's equivalent in list comprehension
    # [item for item in iterable if function(item)]  # if function not None
    # [item for item in iterable if item]  # if function is None

####<a id="map">map</a>

`map(function, sequence)` is a function with two arguments.  The first argument is the name of a function and the second is a _sequence data type_ (e.g. a list).  Map applies the function to all the elements of the sequence and returns this as a new list.

    # map()
    def fahrenheit(T):
        return ((float(9)/5)*T + 32)
    temp = (36.5, 37, 37.5, 39)  # temp (C)
    F = map(fahrenheit, temp)
    
    # map() with lambda
    Celsius = [36.5, 37, 37.5, 39]
    Fahrenheit = map(lambda x: (float(9)/5)*x + 32, Celsius)
    print Fahrenehit  # [97.7, 98.60000000000001, 99.5, 102.2]

####<a id="reduce">reduce</a>

`reduce(function, sequence)` applies a function to a sequence and returns a single value (instead of a list like the above).  The idea is that the first two items are reduced to a single value, then that single value and the third value is reduced into a new single value.  This repeats until no more items are in the list.

    # reduce()
    result = reduce(lambda x,y: x+y, [47, 11, 42, 13])
    print result  # 113

####<a id="zip">zip</a>

`zip(sequence1, sequence2)` returns a list of tuples, where each tuple contains the ith element from each of the sequences.  The returned list is truncated to the length of the shortest argument sequence.

    # zip()
    temp = zip(range(5), range(1, 20, 2))
    print zip  # [(0, 1), (1, 3), (2, 5), (3, 7), (4, 9)]

- - - -

##<a id="debug">DEBUGGING FUNCTIONS</a>

These functions are nice for debugging your code.

####<a id="help">help(object)</a>

`help()` uses the built-in help system to tell you more about the function, class, method, keyword, etc.  Exit help with `:q`.

####<a id="dir">dir()</a>

`dir()` gives you the list of names in the current local scope.  If you input an object as an argument, it tells you its attributes.

####<a id="globals">globals()</a>

`globals()` returns a dictionary of the current objects.

####<a id="type">type(object)</a>

`type()` returns the type of an object (e.g. str, list)

####<a id="id">id(object)</a>

`id()` returns the identity of the object as an integer or long integer; this represents the address of the object in memory.  This is useful to see if copies are being referenced or if the same object is.

- - - -
