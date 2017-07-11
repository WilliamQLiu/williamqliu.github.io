---
layout: post
title: Python Basics
---

## {{ page.title }}

- - - -

**About Python**

Python is a general purpose programming language that focuses on code readability.  This is a quick summary of what you will need to know about Python data types and control flow.

__BEGINNER__

*  [Numbers (int, float)](#numbers)
*  [Sequence Types (i.e. can slice)](#sequencetypes)
    -  [Strings (str) and Unicode (unicode)](#strings)
    -  [Lists (list, stacks, queues, list comprehension)](#lists)
    -  [Tuples](#tuples)
    -  [Sequence Methods (e.g. enumerate)](#sequencemethods)
    -  [Sorting Sequences](#sequencesort)
*  [Hash Table (i.e. key-value pair)](#hashtables)
    -  [Dict](#dict)
    -  [Set](#set)
*  [Control Flow](#controlflow)
    -  [if else statement](#ifelse)
    -  [for statement](#for)
    -  [range(), xrange()](#range)
    -  [lambda expression](#lambda)

__ADVANCED__

*  [Debugging Functions](#debug)
    -  [help()](#help)
    -  [dir()](#dir)
    -  [globals()](#globals)
    -  [type()](#type)
    -  [id()](#id)
    -  [__repr__](#repr)
*  [Mutable and Immutable](#mutableimmutable)
	-  [](#)
*  [Functions, Classes, Methods](#functionmethodclass)
    -  [variable scope](#variablescope)
    -  [bound and unbound methods](#boundunbound)
    -  [class and static methods](#classstaticmethods)
    -  [decorators](#decorators)
*  [Class Objects](#classes)
    -  [Attribute Reference](#classattributes)
    -  [Instantiation and `__init__()`](#init)
    -  [`self` and `cls`](#selfandcls)
    -  [underscores](#underscores)
    -  [inheritance](#inheritance)
    -  [abstract class](#abstractbaseclass)
    -  [`super()`](#super)
*  [Class Magic Methods](#magicmethods)
    -  [Construction and Initialization](#magicmethodsconstruction)
    -  [Representation of Classes](#magicmethodsrepresentation)
    -  [Attribute Access](#magicmethodsattributeaccess)
    -  [descriptors](#descriptors)
    -  [Container Objects](#magicmethodscontainerobjects)
    -  [Callable Objects](#magicmethodscallable)
*  [Context Managers](contextmanagers)
    -  [__enter__() and __exit__()](#enterexit)



- - - -

## <a id="numbers">NUMBERS</a>

Python can do simple calculations like addition, subtraction, division, multiplcation, and calculate powers.  

     2 + 2**3  # Addition with 2 to the power of 2
     #10

#### Floor Division

Integers (e.g. `2`, `4`, `20`) have type **int** and the fractional parts (e.g. `5.0`, `1.6`) have type **float**.  When doing division with a numerator and denominator of type int, then there is an automatic **floor division** (i.e. rounds down to the nearest integer, e.g. `11/4 = 2` even though result should be `2.75`).  To avoid floor division, make one of the operands a float (e.g. `11/4.0` and your result will be `2.75`).  You can also force a floor division (regardless of data types) using `//` operator

     (50 - 5 * 6) / 3  # Floor division because operands are both type int
     #6
     
     (50 - 5 * 6) / 3.0  # No floor division, at least one operand type float
     #6.666666666666667
     
     #(50 - 5 * 6) // 3  # Forces floor division
     #6

- - - -

## <a id="sequencetypes">SEQUENCE TYPES</a>

### <a id="strings">Strings</a>

Python can also manipulate strings using single quotes (`'...'`) and double quotes (`"..."`) with `\` as an escape character.  Strings are **immutable** (i.e. object can't be altered, a new object has to be created if a different value has to be stored)

    temp = 'hello world'  # single quotes
    print temp  # hello world

    temp = "hello world"  # double quotes
    print temp  # hello world
    
    temp = 'doesn\'t'  # use \ to escape a quote
    print temp  # doesn't
    print type(temp)  # <type 'str>'

#### Raw Strings

If you don\'t want characters prefaced by `\` to be interpreted as a special character (e.g. `\n` used in next line), then use `r` before the string

    print "C:\some\name"  # here \n means newline
    #C:\some
    #ame
    
    print r"C:\some\name"  # here it reads the raw string
    #C:\some\name

#### Joining and Slicing Strings

If you want to join strings together use `+`.  If you want to slice strings, use `[]`.

    word = "Python"
    print word[1]  # Get character in position 0
    #y
    
    print word[-1]  # get character in last position
    #n
    
    print word[2:4]  # Slice characters
    #th

#### String Methods

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

### <a id="lists">Lists</a>

Lists are a way to hold items in order.  The items can be different data types or the same type (if it's the same type and you need speed, look up arrays, in particular numpy arrays).  Lists can also be sliced and concatenated (i.e. sequence type).  Lists are also **mutable** (i.e. there are methods to modify the object)

    squares = [1, 4, 9, 16, 25]  # make a list of numbers
    print squares
    #[1, 4, 9, 16, 25]
    
    squares + [36, 49, 64, 'etc']  # Add in another list
    #[1, 4, 9, 16, 25, 36, 49, 64, 'etc']
    
    print squares[2:4]  
    #[9, 16]
    print type(squares)  # <type 'list'>

#### List Slicing

Just like you can index and slice strings, you can do the same for Python's lists.

    squares = [1, 4, 9, 16, 25]
    print squares # [1, 4, 9, 16, 25]
    squares[0]  # 1
    squares[-1]  # 25
    squares[:]  # [1, 4, 9, 16, 25]
    squares[2:4]  # [9, 16]  # only get index 2 and 3 (excludes 4)
    squares[::2] # [1, 9, 25]  # skip every other
    squares[::-1]  # [25, 16, 9, 4, 1] reverse order

#### Nesting Lists

You can nest lists (create lists that contain other lists).

    a = ['a', 'b', 'c', 'd']
    n = [1, 2, 3, 4]
    x = [a, n]
    print x  # [['a', 'b', 'c', 'd'], [1, 2, 3, 4]]
    print x[0]  # ['a', 'b', 'c', 'd']
    print x[0][1]  # b


#### List Methods

*  `list.append(x)` adds an item `x` to the end of the list
*  `list.extend(L)` extends the list by specific items
*  `list.insert(i, x)` inserts an item `x` at a given position `i`
*  `list.remove(x)` removes the first occurrence of the value `x`
*  `list.clear()` removes all items from the list
*  `list.index(x)` returns index in the list whose value is `x` or an error if none found
*  `list.count(x)` returns the number of times `x` appears in the list
*  `list.sort()` sorts the items of the list in place
*  `list.pop([i])` removes the `i` item or last item if not specified

#### Stacks
A **stack** is using a list with first-in, last-out functionality.  The main methods for stacks are `append()` and `pop()`.  Note that with append, the items you add can be other lists (i.e. you can have a list with lists inside as individual items)

#### Queues
A **queue** is a specific list built for efficient first-in, first-out functionality.  For more specialized containers, look up `collections`.

    from collections import deque
    queue = deque(["Eric", "John", "Michael"])
    queue.append("Terry")  # Terry arrives
    queue.append("Graham")  # Graham arrives
    queue.popleft()  # Eric (first in queue) now leaves
    queue.popleft()  # John (second in queue) now leaves
    queue  # deque(['Michael', 'Terry', 'Graham'])
    print type(queue)  # <type 'collections.deque'>

#### List comprehensions

List comprehensions are a concise way of making lists based on an operation or if the item satisifes a certain condition.  This is a compact for-loop that builds lists.  Here are different ways of doing a regular loop along with the list comprehension equivalent:

    sqaures = []
    for x in range(10):  # range(10) loops from 0 - 9
        squares.append(x**2)
    print squares
    #[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
    
    squares = map(lambda x: x**2, range(10))  # same, using map
    squares = [x**2 for x in range(10)]  # same, using list comprehension

#### So what makes up a list comprehension?

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

### <a id="tuples">Tuples</a>

**Tuples** are like read-only lists because they are immutable (instead of mutable) and are enclosed with parentheses (instead of brackets).  Tuples do care about the order of when items were inserted. Tuples can also be sliced just like lists.

So what's the big deal about tuples? Tuples are a fixed size so they don't have append or extend
methods like lists. However, since Tuples are immutable, tuples are great for 
dictionary keys (since dict keys have to be immutable). 

    temp = ('abcd', 786, 2.23, 'john', 70.2 )
    print temp  # ('abcd', 785, 2.23, 'john', 70.2)
    print type(temp)  #<type 'tuple'>
    
    print temp[0]  # prints first element of the tuple
    # abcd
    
    print temp[1:3]  # prints elements after 1st to 3rd
    # (785, 2.23)

So why use a tuple over a list?

*  Certain tuples (if they contain values like strings, numbers, other tuples) can be used as dictionary _keys_.  Lists cannot be used as dictionary keys (because lists are not immutable).  Dictionary _keys_ require an immutable data type, although the _value_ can be mutable or immutable).
*  Tuples are faster than lists.

Here is a code example of a tuple holding structure:

    my_book_location = ("Lord of te Rings", 41, 11)  # book, page number, line number
    my_book_location[0]  # "Lord of the Rings"

    clicked_here = (10, 100)  # clicked on x, y

    # Use with hash tables
    clicked_table = {}
    clicked_table[clicked_here] = "clicked on monkey" 
    clicked_table[clicked_here] = "clicked on monkey"

Notes about tuples:

* To write a tuple with a single value, you need to include a comma, e.g.: `tup1 = (50, )`
* You cannot update a tuple, you can only delete the entire tuple
* Tuples allow different data types (e.g. str, int) together
* Tuples allow slicing just like lists
* Tuples can be used as a dictionary key as long as all elements are immutable
  (e.g. no list in there like `my_tuple = ("hey", [1, 2])`

#### <a id="sequencemethods">Sequence Methods</a>
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

#### Enumerate

A useful built-in function for sequences is Enumerate.  Enumerate means you return a tuple containing a count and the value from iterating over the sequence.  By default this starts counting from start 0, unless you specify the `start=` parameter.

    seasons = ['Spring', 'Summer', 'Fall', 'Winter']
    
    list(enumerate(seasons))
    # [(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
    
    list(enumerate(seasons, start=1))
    # [(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
    
    for i, value in enumerate(seasons):
        print "Position ", i, " has value ", value
    #Position  0  has value  Spring
    #Position  1  has value  Summer
    #Position  2  has value  Fall
    #Position  3  has value  Winter

#### <a id="sequencesort">Sorting Sequences</a>

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

#### keys

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

#### operator (itemgetter, attrgetter, methodcaller)

Python has a few built in functions to help you get get items with `itemgetter` and `attrgetter` to get attributes

    from operator import itemgetter, attrgetter

#### itemgetter

Get an item from an object using **itemgetter**.  This basically looks up an index on a sequence, then returns that value.

    #String
    itemgetter(1)('ABCDEFG')  #'B'
    
    #List
    itemgetter(2)(['Hello', 'World', 'Will'])  #'World'
    
    #Dict
    itemgetter('Age')(phonebook1)
    
    #Tuple
    itemgetter(2)(student_tuple)  # ('dave', 'B', 10)

#### attrgetter

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

## <a id="hashtables">HASH TABLES</a>

A hash table (aka hash map) is a way to map keys to values (i.e. a key-value pair).  A hash function computes an index into an array of buckets or slots, from which the correct value can be found.

### <a id="dict">Dictionary (dict)</a>

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

### <a id="set">Set</a>

A **set** is an unordered collection of distinct hashable elements.  Note that items have to be hashable and can't hold duplicates. Sets do not have indexing, ordering, or slicing. Sets have all these restrictions, but are really fast. They're also good for set methods.

    from sets import Set
    engineers = Set(['John', 'Jane', 'Jack'])
    print engineers  # Set(['Jane', 'John', 'Jack'])
    print type(engineers)  # <class 'sets.Set'>

    # Set operations
    s1 = set(['Beta', 'Gamma', 'Alpha', 'Delta', 'Gamma', 'Beta'])
    s2 = set(['Beta', 'Alpha', 'Epsilon', 'Omega'])
    len(s1)  # get length of s1, # 4
    'Gamma' in s1  # True  # test value if it exists in s1
    'Beta' not in s1  # False  # test value if it does not exist in s1
    s1.union(s2)  # set(['Epsilon', 'Beta', 'Delta', 'Alpha', 'Omega', 'Gamma'])
    s1 | s2  # set(['Epsilon', 'Beta', 'Delta', 'Alpha', 'Omega', 'Gamma'])
    s1.intersection(s2)  # set(['Alpha', 'Beta'])
    s1.difference(s2)  # set(['Gamma', 'Delta'])
    s1.symmetric_difference(s2)  # set(['Epsilon', 'Delta', 'Omega', 'Gamma'])

- - - -

## <a id="controlflow">Control Flow</a>

Python has the usual control flow statements.  These include:

*  `if` statement
*  `for` statement
*  `range()` function iterates over a sequence of numbers
*  `xrange()` function is like `range()`, but doesn't store in memory
*  `lambda` expression is a small anonymous function, which means lambda: a if some_condition() else b
*  `map()` function applys a function to every item of iterable and returns a list

### <a id="ifelse">if, elif, else statement</a>

    # if statement
    if x < 0:
        print "x is negative"
    elif x==0:
        print "x is zero"
    else:
        print "x is positive"

### <a id="for">for statement</a>

    # for statement
    words = ['cat', 'window', 'defense']
    for w in words:
       print w,  # cat window defense

### <a id="range">range(), xrange()</a>

    # range() function
    for i in range(5):
        print i,  # 0 1 2 3 4
    
    # normal call with separate arguments
    temp = list(range(3, 6))
    print temp  # [3, 4, 5]
    
    $ xrange() function
    for i in xrange(2, 10):
        print i,  # 2 3 4 5 6 7 8 9

### <a id="lambda">lambda expression</a>

Lambda is a way to create small anonymous functions (i.e. a function without a name).  These are often used in combination with `filter()`, `map()`, and `reduce()`

    # a general lambda expression
    lambda x: 'big' if x > 100 else 'small'
    lambda x: x**2

#### <a id="filter">filter()</a>

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

#### <a id="map">map</a>

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

#### <a id="reduce">reduce</a>

`reduce(function, sequence)` applies a function to a sequence and returns a single value (instead of a list like the above).  The idea is that the first two items are reduced to a single value, then that single value and the third value is reduced into a new single value.  This repeats until no more items are in the list.

    # reduce()
    result = reduce(lambda x,y: x+y, [47, 11, 42, 13])
    print result  # 113

#### <a id="zip">zip</a>

`zip(sequence1, sequence2)` returns a list of tuples, where each tuple contains the ith element from each of the sequences.  The returned list is truncated to the length of the shortest argument sequence.

    # zip()
    temp = zip(range(5), range(1, 20, 2))
    print zip  # [(0, 1), (1, 3), (2, 5), (3, 7), (4, 9)]

- - - -

## <a id="debug">DEBUGGING FUNCTIONS</a>

These functions are nice for debugging your code.

#### <a id="help">help(object)</a>

`help()` uses the built-in help system to tell you more about the function, class, method, keyword, etc.  Exit help with `:q`.

#### <a id="dir">dir()</a>

`dir()` gives you the list of names in the current local scope.  If you input an object as an argument, it tells you its attributes.

#### <a id="globals">globals()</a>

`globals()` returns a dictionary of the current objects.

#### <a id="type">type(object)</a>

`type()` returns the type of an object (e.g. str, list)

#### <a id="id">id(object)</a>

`id()` returns the identity of the object as an integer or long integer; this represents the address of the object in memory.  This is useful to see if copies are being referenced or if the same object is.

#### <a id="repr">__repr__</a>

Similar to looking at the id() of an object, you can also use `__repr__` to see the representation of an object.
With the `__repr__` command, you can see if an object is immutable or not.

A way to see if an object is immutable (e.g. a string) is by seeing memory locations change when the object changes.

    >>mystr = "hey there"
    >>mystr.__repr__
    <method-wrapper '__repr__' of str object at 0x7f74135cb5f0>
	# See the memory location where our object mystr is stored
	
	>>mystr = "hey I changed!"
	>>mystr.__repr__
    <method-wrapper '__repr__' of str object at 0x7f74135cb5f0>
	# See that the object is now stored in a different memory location 

	>>mystr_2 = mystr
	>>mystr_2
	"hey I changed"
	>>mystr_2.__repr__
    <method-wrapper '__repr__' of str object at 0x7f74135cb5f0>
	# Notice that when we assign a new variable, it is pointing at the same memory location as the other variable

	>>mystr_2 = "Another new value"
	>>mystr_2.__repr__
	<method-wrapper '__repr__' of str object at 0x7f7413541cd8>
	# Only when we change the value of our new variable does it point to a new memory location

A way to see if an object is mutable (e.g. a list) is by seeing memory locations stay the same even when the object changes:
	
	>>mylist = [1, "hey", "there", None]
	>>mylist.__repr__
	<method-wrapper '__repr__' of list object at 0x7f7413546248>
	# Note the memory location where our object mylist is stored

	>>mylist.append("another item")
	>>mylist.__repr__
	<method-wrapper '__repr__' of list object at 0x7f7413546248>
	# Using built-in append does not change the memory location

	>>mylist[2]
	"there"
	>>mylist[2] = "boo"
	>>mylist
	[1, "hey", "boo", None, "another item"]
	>>mylist.__repr__
	<method-wrapper '__repr__' of list object at 0x7f7413546248>
	# Mutability means when parts of the list are changed, does not need to recreate, still same memory location

	>>anotherlist = mylist
	>>anotherlist
	[1, "hey", "boo", None, "another item"]
	>>mylist
	[1, "hey", "boo", None, "another item"]
	>>anotherlist.__repr__
	<method-wrapper '__repr__' of list object at 0x7f7413546248>
	# Notice that both lists are in the same memory location
	
	>>anotherlist[2] = "foo"
	>>anotherlist
	[1, "hey", "foo", None, "another item"]
	>>mylist
	[1, "hey", "foo", None, "another item"]
	# Notice that both lists have now changed, even though we only modified anotherlist
	
	>>anotherlist.__repr__
	<method-wrapper '__repr__' of list object at 0x7f7413546248>
	>>mylist.__repr__
	<method-wrapper '__repr__' of list object at 0x7f7413546248>


## <a id="mutableimmutable">Mutable and Immutable</a>

So what does this all mean by mutable and immutable? This concept helps you know if an object is being updated or if you are returning a new object. In order to write a working and efficient program, you need to know the difference. Also, only immutables are hashable (therefore members of **sets** or keys in **dicts**, and since hash tables are the quickest lookup, its very important).

### <a id="mutableimmutablegeneral">General Rule</a>

Usually if the type is primitive (e.g. string), the object is immutable and if the type is
a container (e.g. a list), the object is mutable. We have:

Immutable Objects:

* int
* float
* decimal
* complex
* bool
* string
* tuple
* range
* frozenset
* bytes

Mutable objects (usually containers, user-defined types):

* list
* dict
* set
* bytearray (used if you want in-place modification of a string)
* user-defined classes (unless specifically made immutable)

The exceptions are a __tuple__, which is an immutable container and
a __frozenset__, which is an immutable version of set. 

### <a id="mutableimmutableerror">Common Error</a>

So you want to avoid putting a mutable object as the default value of
a function parameter (immutable types are perfectly safe).

    def my_function_wrong(param=[]):
        param.append("thing")
        return param

    my_function_wrong()  # returns ["thing"]
    my_function_wrong()  # returns ["thing", "thing"]
    # what actually happens is that since we passed a mutable object, we are
    using the same list (Python only evaluates functions once)

    # Instead what you want to do is this:
    def my_function_correct(param=None):
        if param is None:
            param = []
        param.append("thing")
        return param


### <a id="mutableimmutableefficiency">Efficiency</a>

You can concatenate two strings together like this:

	string_build = ""
	for data in container:
		string_build += str(data)

Because strings are immutable, concatenating two strings together creates a third string, which is the combination of two strings.

Instead, you want to do something that takes advantage of the mutability of a single list object to gather data together and then allocate a single result string to put your data in:

	builder_list = []
	for data in container:
		builder_list.append(str(data))
	"".join(builder_list)

	### Another way is to use a list comprehension
	"".join([str(data) for data in container])
	
	### Or use a map function
	"".join(map(str, container))



- - - -

## <a id="functionmethodclass">Functions, Classes, and Methods</a>

A __function__ is a block of reusable code that is used to perform specific actions.  Some familiar built-in functions are things like `print`.  You can do a variety of things including pass in arguments, set default values, set keyword arguments, and make certain fields required.  Functions are defined using the `def` keyword.

    def happy_birthday(name, age_yesterday=10):
        """ Print happy birthday to a person and tell them their age"""
        age = age_yesterday + 1
        message = "Happy Birthday " + person + "! Congrats turning " + str(age)
        return message
    
    happy_birthday(Will, 29)  # Happy Birthday Will! Congrats turning 30!
    happy_birthday('Laura', 26)  # Happy Birthday Laura! Congrats turning 27!
    happy_birthday(name='Billy')  # Happy Birthday Billy! Congrats turning 11!

A __class__ is like a blueprint or instruction manual for creating objects.  You define a class using the `class` keyword and normally name it starting with a capital letter (e.g. Person, Car).  Even though we define how to create an object, we do not actually create an instance of one until we call the Class (e.g. Will is an instance of a Person, a Tesla is an instance of a Car).

    class Customer(object):
        """ A customer of a bank with a checking account """
        def __init__(self, name, balance=0.0):
            """ Return a Customer object with name and balance """
            self.name = name
            self.balance = balance
        
        def withdraw(self, amount):
            """ Return the balance remaining after withdrawing an amount """
            if amount > self.balance:
                raise RuntimeError("Amount greater than available balance")
            self.balance -= amount
            return self.balance
        
        def deposit(self, amount):
            """ Return balance remaining after depositing an amount """
            self.balance += amount
            return self.balance
    
    will = Customer()  # Create an instance of the Class

A __method__ is a function that is stored as a class attribute.  Here we declare a method called `get_size` and bind it to an instance of our object Pizza.

    class Pizza(object):
        def __init__(self, size):
            self.size = size
        def get_size(self):
            return self.size
    
    my_pizza = Pizza(size=5)
    my_pizza.get_size()  # 5

#### <a id="variablescope">Local vs Global variables</a>

Variables defined inside a function have a __local scope__, which means that they can only be accessed inside the function they are declared.

    def calculate(arg1, arg2):
        """ Add both the parameters and return the total """
        total = arg1 + arg2
        print "Inside the local function, total is:", total
        return total
    calculate(5, 7)
    #Inside the local function, total is: 12

Variables defined outside a function have a __global scope__, which means that they can be accessed outside the function.

    a = 10
    def my_func():
        a = 5
        return a
    print a  # 10
    print my_func()  #5

#### <a id="boundunbound">Bound vs Unbound methods</a>

In Python classes we have __unbound__ and __bound__ methods.  When you access the method directly through the class (e.g. `Customer.withdraw()`), it is __unbound__.  When you access the method through an instance of the class (e.g. `Will.withdraw()`, it is __bound__ to that instance of the class (in our example, the Customer `Will`).  This means that when you call a bound instance, its simply saying `Will` is passed as the first argument.

     class Customer(object):
        def withdraw():
            return "Withdrawing money!"
    
    # Example of unbound method
    Customer.withdraw
    # <unbound method Customer.withdraw>
    
    # Example of bound method
    Will = Customer()
    Will.withdraw
    # <bound method Customer.withdraw of <__main__.Customer object at 0x00000000>>

#### <a id="classstaticmethods">Class and Static Methods</a>

Besides bound and unbound methods, we have __static methods__ and __class methods__.

    class Customer(object):
        @staticmethod
        def withdraw():
            return "Withdrawing money!"
        @classmethod
        def deposit(cls):
            return "Depositing money!"
    
    Will = Customer()
    Will.withdraw  # <function withdraw at 0x00000000>
    Will.withdraw()  # Normally this would be a bound method, but staticmethod prevents it
    # 'Withdrawing money!'
    
    Will.deposit  # <bound method type.deposit of <class '__main__.Customer'>>
    Will.deposit()  # 'Depositing money!'

__Static methods__ tells the method not to bind to an instance.  For the example, the method would normally be a bound method, but the `@staticmethod` makes this into a regular function.  When you see `@staticmethod`, it tells us that the method does not depend on the state of the object itself.

__Class methods__ tells the method not to bind to an instance, but the `@classmethod` binds the method to a class.  A class method is used to share among all the instances.

#### <a id="decorators">Decorators</a>

Decorators are functions that are applied right before your function or method call.  This is usually seen in something like a login function where say you can only run this specific function if you are logged in.  The decorator is a clean way to show this so you do not have to declare the login function inside your function. 

    def p_decorate(func):
        """ Wraps string around with <p> tags """
        def func_wrapper(name):
            return "<p>{0}</p>".format(func(name))
        return func_wrapper
    
    # Example of decorating a function
    @p_decorate
    def get_text_clean(name):
        return "lorem ipsum, {0} dolor sit amet".format(name)
    
    # Example of decorating a method
    class Person(object):
        def __init__(self):
            self.name = "Willy"
            self.family = "Liu"
    
        @p_decorate
        def get_fullname(self):
            return self.name + " " + self.family


- - - -

## <a id="classes">CLASS OBJECTS</a>

Classes in Python create objects, which provides all the standard features of Object Oriented Programming.  You can have class inheritance from base class(es); this means you can derive a new class with all the variables and methods of the base class(es) and override any variables and/or methods.  We can access Class objects through __attribute reference__ or __instantiation__.  By convention, we upper case our class names (e.g. Pet).

#### <a id="classattributes">Class Attribute Reference</a>

When you create a Class, all variables are in the __local scope__ (i.e. referenced only inside the Class).  You can reference class objects with the pattern `Obj.name`; this can reference variables or methods.  

Example 1

    class Car(object):
        """ This is the docstring of a class """
    
        wheels = 4
        def __init__(self, make, model):
            self.make = make
            self.model = model
    
        def make_sound(self):
            print "Vroooom!"
    
    mustang = Car('Ford', 'Mustang')
    print mustang.wheels  # 4
    print Car.wheels  # 4

Example 2

    class MyClass:
        """ A simple example class """
        i = 12345  # a class attribute set at the class level
    
        def f(self):
            return 'hello world'
    
    MyClass.i  # access an object integer
    # 12345
    
    MyClass.f  # access an object method
    # <unbound method MyClass.f>
    
    MyClass.__doc__  # access an object docstring
    # 'A simple example class'
    
    MyClass.__dict__  # access an object dict
    # {'i': 12345, '__module__': '__main__', '__doc__': ' A simple class ', 'f': <function f at 0x0000000002BCA128>}

#### <a id="init">Instantiation using `__init__(self)`</a>

Class __instantiation__ uses function notation; just pretend a class object is a parameterless function that returns a new instance of the class.

    x = MyClass()  # creates a new instance of the class and assigns it to a variable x

The instantiation operation ('calling' a class object) creates an empty object.  If you want to create an initial state, you define `__init__(self)`.  A good rule of thumb is to never introduce a normal variable outside of `__init__`.

    class Complex:
        def __init__(self, realpart, imagpart):
            self.r = realpart
            self.i = imagpart
    
    x = Complex(3.0, -4.5)
    x.r, x.i  #(3.0, -4.5)

#### <a id="selfandcls">`self` and `cls`</a>

`self` tells us we are looking at an _instance method_.  The name is by convention and does not actually have to be self (i.e. can be any word).  What it comes down to is when you call a method of an instance (e.g. call getName method from the instance of polly from the class Pet), Python automatically figures out that self should be the instance (e.g. polly) and passes it to the function (e.g. getName).  Always use `self` for the first argument to instance methods.

    class Pet(object):
        def __init__(self, name, species):
            self.name = name
            self.species = species
        def getName(self):
            return self.name
        def getSpecies(self):
            return self.species
        def __str__(self):
            return "%s is a %s" % (self.name, self.species)
    
    polly = Pet("Polly", "Parrot")
    
    print "Polly is a %s" % polly.getSpecies()
    # Polly is a Parrot
    
    print "Polly is a %s" % Pet.getSpecies(polly)
    # Polly is a Parrot

`cls` tells us we are looking at a _class method_.  This is similar to how self is used in instance methods.  Always use cls for the first argument to class methods.  Again, this naming is just convention and in theory you can call this whatever you want (but don't make a new name, this will just cause confusion).  The reason you use cls is if you wanted attributes that all instances of the class can share.  cls is usually used in the `__new__` protocol for `staticmethod` and `classmethod` (i.e. methods that only need access to the class, but not to things specific to each instance of the class).

#### <a id="underscores">Use of underscores `_` and `__`</a>

With classes, you may have noticed that some variables and functions have `_` and `__` listed in front (e.g. `__init__`, `__del__`).  Here are what these mean:

*  `_singleleadingunderscore` - a single leading underscore usually means an 'internal use' indicator (i.e. this field is used only in this context, you should not access it).  This is more than just convention; when a class is imported, the objects that start with an `_` are not imported.
*  `singletrailingunderscore_` - a single trailing underscore means avoiding a conflict with a Python keyword (e.g. class is now class_)
*  `__doubleleadingunderscore` - double leading underscores mean that the variables should be private and you should not access them (but really you still can since this is Python).  The idea is that subclasses should not accidentally override the private methods and attributes of their superclasses so Python does __name mangling__, which creates a class attribute in the pattern `_classname__spam`.
*  `__doubleleadingandtrailunderscore__` - double leading and trailing underscores is reserved for Python's magic/builtin methods or variables.  Do not create these, just use them as documented (e.g. `__init__`, `__doc__`).  See the magic methods section for more on this.

#### <a id="inheritance">Class Inheritance (aka Derived Class, Subclass)</a>

Classes can support __inheritance__, which can include __single inheritance__ and __multiple inheritance__ (Note: avoid multiple inheritance, this gets too complicated).  The idea is that for the code `class Dog(Pet)`, a class named `Dog` inherits the features from the parent class `Pet`.

Classes can be derived from other classes and these can take many forms.  A __derived class__ (aka __subclass)__ is a class that is derived from another class.  As soon as we create a class, we are actually already subclassing from an object (e.g. `class Human(object):`).  A more obvious example of inheritance would be a class _Car_ and class _Truck_ inheriting from class _Vehicle_, (`class Car(Vehicle)`, `class Truck(Vehicle)`).  For example:

    class Vehicle(object):
        """ A vehicle for sale """
    
        base_sale_price = 0
    
        def __init__(self, wheels, miles, make, model, year, sold_on):
            """ Return a new Vehicle object """
            self.wheels = wheels
            self.miles = miles
            self.make = make
            self.model = model
            self.year = year
            self.sold_on = sold_on
    
        def sale_price(self):
            """ Return the sale price for this vehicle """
            if self.sold_on is not None:
                return 0.0  # Already sold
            return 5000.0 * self.wheels
    
        def purchase_price(self):
            """ Return the price we would pay to purchase the vehicle """
            if self.sold_on is None:
                return 0.0  # Not yet sold
            return self.base_sale_price - (.10 * self.miles)
    
    class Car(Vehicle):
        def __init__(self, wheels, miles, make, model, year, sold_on):
            """ Return a new Car object """
            self.wheels = wheels
            self.miles = miles
            self.make = make
            self.model = model
            ...
    
    class Truck(Vehicle):
        def __init__(self, wheels, miles, make, model, year, sold_on):
            """ Return a new Car object """
            self.wheels = wheels
            self.miles = miles
            self.make = make
            self.model = model
            ...

#### <a id="abstractbaseclass">Abstract Base Class</a>

In the above, we have a few issues in that there is still some repetition of fields and now we have a _Vehicle_ object when it is really a concept (and not a real thing).  We can abstract these methods with `from abc import ABCMeta, abstractmethod`.  For example:

    from abc import ABCMeta, abstractmethod
    
    class Vehicle(object):
        """ Vehicle for Sale """
    
        __metaclass__ = ABCMeta
        
        base_sale_price = 0
        wheels = 0
    
        def __init__(self, miles, make, model, year, sold_on):
            self.miles = miles
            self.make = make
            self.model = model
            self.year = year
            self.sold_on = sold_on
    
        def sale_price(self):
            """Return sale price"""
            if self.sold_on is not None:
                return 0.0  # Already sold
            return 5000.0 * self.wheels
        
        def purchase_price(self):
            """ Return price we pay to purchase the vehicle """
            if self.sold_on is None:
                return 0.0  # Not yet sold
            return self.base_sale_price - (.10 * self.miles)
    
        @abstractmethod
        def vehicle_type(self):
            """ Return a string representing the type of vehicle this is """
            pass
    
    class Car(Vehicle):
        """ A Car """
    
        base_sale_price = 8000
        wheels = 4
    
        def vehicle_type(self):
            """ Return a string representing the type of vehicle this is """
            return 'car'
    
    class Motorcycle(Vehicle):
        """ A Motorcycle """
    
        base_sale_price = 4000
        wheels = 2
    
        def vehicle_type(self):
            """ Return a string representing the type of vehicle this is """
            return 'motorcycle'

__Abstract Base Class__ creates a class that you can only inherit from.  By putting `__metaclass__ = ABC`, we created an ABC.

__abstractmethod__ requires that the class has a `__metaclass__ = ABCMeta`. 

#### <a id="reflection">Reflection (aka Introspection)</a>

__Reflection__ (aka __introspection__) means finding out about the type, class, attributes and methods of an object.  Some reflection-enabling functions include:

*  `type()`
*  `isinstance()` - check if this is an instance of a class; checks attribute `class.__instancecheck__(self, instance)`.  E.g. `isinstance(instance, class)`. A good use is `isinstance(mystring, str)`
*  `issubclass()` - check if this is a subclass of a class; checks attribute `class.__subclasscheck__(self, subclass)`.  E.g. `issubclass(subclass, class)`
*  `callable()` - check if this instance of your class can be called as if it were a function.
*  `dir()`
*  `getattr()`

#### <a id="super">Superclass and `super()`</a>

With class inheritance, you can have many hierarchies.  For example, if C is a subclass of C1 and C1 is a subclass of C2, we have the linear hierarchy of [C, C1, C2].  With multiple inheritance this can get pretty crazy with no linearization (so try to avoid using multiple inheritance).  The set of rules that construct this linearization of C is called the __Method Resolution Order (MRO)__ (aka __C3 superclass linerization__) and can be found under the __mro__ attribute.

We can use `super()` to get the class hierarchy when there is single inheritance without having to name the parent classes explicitly.

    class C(B):
        def method(self, arg):
            super().method(arg)  # same as super(C, self).method(arg)

#### <a id="metaclasses">Metaclasses and `type`</a>

So we know that classes are templates to create objects.  We can also have templates to build classes; these are called __metaclasses__.  So basically, classes are instances of something called metaclasses (the idea is kind of like how we have instances of classes).  The base metaclass is an instance of `type` (which is a kind of object).  To create this, you can do:

     class mymeta(type):
         pass

We don't really need to use metaclasses or type.

## <a id="magicmethods">Class Magic Methods</a>

Python has a few 'magic' methods that add 'magic' to your classes.  These methods range from helping with initialization, showing how classes are represented, and what attributes are accessed.  This is a more in-depth and advanced look into how classes work in Python.

#### <a id="magicmethodsconstruction">Construction and Initialization</a>

`__new__(cls, ...)` - this is the first method called when a object is instantiated.  It takes the class and any arguments and passes this to `__init__`.  This is not used too often except for sublassing an immutable type (like a tuple or string).

`__init__(self, ...)` - this is the initializer for a class; it gets passed when a new instance is created.  For example, `will = Person('William', 30)` would pass in the values `William` and `30` as arguments.

`__del__(self)` - this is destructor; this defines behavior for when an object is garbaged collected (e.g. close a database connection, close a file object).  There is no guarantee that this will be executed on exit so be careful.

    from os.path import join
    
    class FileObject:
        """ Wrapper for file objects """
        def __init__(self, filepath='~', filename='sample.txt'):
            # open a file filename in filepath in read and write mode
            self.file = open(join(filepath, filename), 'r+')
        def __del__(self):
            self.file.close()
            del self.file

#### <a id="magicmethodsrepresentation">Representation of Class</a>

You can represent your class usually as a custom string.

`__str__(self)` - this represents what happens when `str()` is called on the instance of your class.  Usually this returns a useful human readable name.

`__dir__(self)` - this represents what happens when `dir()` is called on the instance of your class.  Usually you do not modify this, it just returns a list of attributes of your class.

#### <a id="magicmethodsattributeaccess">Attribute Access</a>

Some say that Python does not have true encapsulation for classes (e.g. no way to define private attributes and then have a public getter and setters).  Python actually does have a lot of encapsulation through these 'magic' methods.

`__getattr__(self, name)` - this represents what happens when a user attempts to access an attribute that does not exist.  This is commonly used in catching and redirecting common misspellings, giving warnings about deprecated attributes or methods.

`__setattr__(self, name, value)` - this assigns a value to an attribute regardless of whether or not that attribute exists.

`__delattr__(self, name)` - this attempts to delete an attribute regardless of whether or not that attribute exists.

`__getattribute__(self, name)` - try to avoid this and use `__getattr__` instead since this has a higher chance of implementing infinite recursion.

## <a id="descriptors">Descriptors</a>

__Descriptors__ are classes that have any of the methods `__get__()`, `__set__()`, and/or `__delete__()` defined.  The default behavior for these attribute access is to get, set, or delete the attribute from an object's dictionary.  When you define your own method, you override the default behavior.  So why would you want to do that?  Descriptors are what's used in defining properties, (bound and unbound) methods, static methods, class methods, and `super()`.

__Descriptor Protocol__

*  `descr.__get__(self, obj, type=None)` --> value
*  `descr.__set__(self, obj, value)` --> None
*  `descr.__delete__(self, obj)` --> None

Depending on what methods you define/override, this results in different types of descriptors (and this type determines how overrides are calculated).

*  If you only define `__get__()`, this is a __non-data descriptor__.  This is typically used for methods
*  If you define both `__get__()` and `__set__()`, this is a __data descriptor__.

__Invoking Descriptors__

Descriptors are not meant to be used alone and instead are used when building object-oriented databases or classes that have attributes whose values are dependent on each other.

    class Meter(object):
        ''' Descriptor for a meter '''
    
        def __init__(self, value=0.0):
            self.value = float(value)
        def __get__(self, instance, owner):
            return self.value
        def __set__(self, instance, value):
            self.value = float(value)
    
    class Distance(object):
        ''' Class to represent distance using descriptor for meters '''
        meter = Meter()

__property()__

Calling __property()__ is a quick (and recommended) way to access or set an attribute.  When you want read-only properties, then use the `@property` decorator.

#### <a id="magicmethodscontainerobjects">Container Objects</a>

We can implement __container objects__, these can be __sequences__ (like lists, tuples) or __mappings__ (like dictionaries) or any other container type.  Container objects can be:

*  An __immutable container__ requires:
    -  `__len__`
    -  `__getitem__`
*  A __mutable container__ requires:
    -  `__len__`
    -  `__getitem__`
    -  `__setitem__`
    -  `__delitem__`
*  An __iterable__ requires:
    -  `__iter__`
    -  `__next__`

As for the actual protocols, here is a short (and not complete) list:

`__len__(self)` - returns the length of the container; used on both immutable and mutable containers.

`__getitem__(self, key)` - defines behavior for when an item is accessed.  The notation is: `self[key]`.  This raises `TypeError` if the type of the key is wrong and `KeyError` if there is no corresponding value for the key.

`__setitem__(self, key, value)` - defines behavior for when an item is assigned.  The notation is `self[nkey] = value`.  This raises `TypeError` if the type of the key is wrong and `KeyError` if there is no corresponding value for the key.

`__delitem__(self, key)` - defines behavior for when an item is deleted.  The notation is `del self[key]`.  The notation is `self[nkey] = value`.  This raises `TypeError` if the type of the key is wrong and `KeyError` if there is no corresponding value for the key.

`__iter__(self)` - is an iterator for the container.  For example `for x in container:`.  Iterators are their own objects and they also must define an `__iter__` method that returns `self`.

#### <a id="magicmethodscallable">Callable Objects</a>

Python functions are first-classed objects; this means they can be passed to functions and methods as if they were objects of any other kind.  A special magic method `__call__(self, ...)` allows an instance of the class to behave as if they were functions.  This means `x()` is the same as `x.__call__`.  This is useful in classes whose instances need to change an object's state.

    class Entity(object):
        """ Class to represent an entity.  Callable to update the entity's position.  If no class inheritance, it is good practice to explicitly inherit from type 'object' """
    
        def __init__(self, size, x, y):
            self.x, self.y = x, y
            self.size = size
    
        def __call__(self, x, y):
            """ Change the position of the entity """
            self.x, self.y = x, y

#### <a id="newstyleoldstyle">New Style Classes and Old Style Classes</a>

Don't worry about this if you're using a current version of Python (e.g. 2.7 or 3+).  You're using a new style object or class (Note: a class is new style if it inherits from `object` or `type`).

## <a id="contextmanagers">Context Managers</a>

PEP 343 introduces a new __with__ statement to Python so we don't have to be as reliant on __try/finally__ statements. we have context managers that provide __enter__() and __exit__() methods that are invoked on entry to and exit from the body of the __with__ statement.  For example, the below statement is roughly translated to:

    with VAR = EXPR:
        BLOCK
    
    VAR = EXPR
    VAR.__enter__()
    try:
        BLOCK
    finally:
        VAR.__exit__()

