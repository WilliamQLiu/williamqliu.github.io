---
layout: post
title: Python Basics
---

## {{ page.title }}

- - - -

**About Python**

Python is a general purpose programming language that focuses on code readability.  This is a quick summary of what you will need to know about Python data types and control flow.

*  Numbers (int, float)
*  Sequence Types (i.e. can slice)
    *  Strings (str) and Unicode (unicode)
    *  Lists (list, list comprehension)
    *  Tuples
*  Hash Table Types (i.e. key-value pair)
    *  Dicts
    *  Sets
*  Control Flow
    *  if statement
    *  for statement
    *  range()
    *  lambda expression
*  Debugging Functions
    *  help()
    *  dir()
    *  globals()
    *  type()
    *  id()

- - - -

## NUMBERS

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

## SEQUENCE TYPES

###Strings

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

###Lists

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

###Tuples

**Tuples** are like read-only lists because they are immutable (instead of mutable) and are enclosed with parentheses (instead of brackets).

    temp = ('abcd', 786, 2.23, 'john', 70.2 )
    print temp  # ('abcd', 785, 2.23, 'john', 70.2)
    print type(temp)  #<type 'tuple'>
    
    print temp[0]  # prints first element of the tuple
    # abcd
    
    print temp[1:3]  # prints elements after 1st to 3rd
    # (785, 2.23)

#### Sequence Functions
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

- - - -

## HASH TABLE TYPES
A hash table (aka hash map) is a way to map keys to values (i.e. a key-value pair).  A hash function computes an index into an array of buckets or slots, from which the correct value can be found.

###Dictionary (dict)
A **dictionary** is a key-value pair.  The key can be almost any type, but usually are numbers or strings.  Value can be any Python object.  Dictionaries are enclosed with `{}` and accessed with brackets `[]`

    phonebook = {}  # Create dict with {}
    phonebook["John"] = 938477566  # Get value with []
    phonebook["Jack"] = 938377264
    print phonebook  # {'John': 938477566, 'Jack': 938377264}
    for name, number in phonebook.iteritems():
        print "Phone number of %s is %d" % (name, number)
    #Phone number of John is 938477566
    #Phone number of Jack is 938377264

- - - -

###Set

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

## CONTROL FLOW

Python has the usual control flow statements.  These include:

*  `if` statement
*  `for` statement
*  `range()` function iterates over a sequence of numbers
*  `xrange()` function is like `range()`, but doesn't store in memory
*  `lambda` expression is a small anonymous function, which means lambda: a if some_condition() else b
*  `map()` function applys a function to every item of iterable and returns a list

###if, elif, else statement

    # if statement
    if x < 0:
        print "x is negative"
    elif x==0:
        print "x is zero"
    else:
        print "x is positive"

###for statement

    # for statement
    words = ['cat', 'window', 'defense']
    for w in words:
       print w,  # cat window defense

###range(), xrange()

    # range() function
    for i in range(5):
        print i,  # 0 1 2 3 4
    
    # normal call with separate arguments
    temp = list(range(3, 6))
    print temp  # [3, 4, 5]
    
    $ xrange() function
    for i in xrange(2, 10):
        print i,  # 2 3 4 5 6 7 8 9

###lambda expression

Lambda is a way to create small anonymous functions (i.e. a function without a name).  These are often used in combination with `filter()`, `map()`, and `reduce()`

    # a general lambda expression
    lambda x: 'big' if x > 100 else 'small'
    lambda x: x**2

####filter()

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

####map()

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

####reduce()

`reduce(function, sequence)` applies a function to a sequence and returns a single value (instead of a list like the above).  The idea is that the first two items are reduced to a single value, then that single value and the third value is reduced into a new single value.  This repeats until no more items are in the list.

    # reduce()
    result = reduce(lambda x,y: x+y, [47, 11, 42, 13])
    print result  # 113

####zip()

`zip(sequence1, sequence2)` returns a list of tuples, where each tuple contains the ith element from each of the sequences.  The returned list is truncated to the length of the shortest argument sequence.

    # zip()
    temp = zip(range(5), range(1, 20, 2))
    print zip  # [(0, 1), (1, 3), (2, 5), (3, 7), (4, 9)]

- - - -

## DEBUGGING FUNCTIONS

These functions are nice for debugging your code.

####help(object)

`help()` uses the built-in help system to tell you more about the function, class, method, keyword, etc.  Exit help with `:q`.

####dir()

`dir()` gives you the list of names in the current local scope.  If you input an object as an argument, it tells you its attributes.

####globals()

`globals()` returns a dictionary of the current objects.

####type(object)

`type()` returns the type of an object (e.g. str, list)

####id(object)

`id()` returns the identity of the object as an integer or long integer; this represents the address of the object in memory.  This is useful to see if copies are being referenced or if the same object is.

- - - -
