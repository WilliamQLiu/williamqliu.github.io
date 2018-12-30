---
layout: post
title: Algorithms and Data Structures
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
    - [Structure of Data](#datastructure)
    - [What is an Algorithm](#whatisalgorithm)
    - [Is the algorithm correct?](#algorithmcorrect)
    - [Is the algorithm efficient?](#algorithmefficient)
    - [Is a sorting algorithm stable?](#algorithmstable)
*  [Big O](#bigo)
    -  [Big O Example](#bigoexample)
    -  [Evaluating Runtimes](#bigotime)
    -  [Asymptotic Notation](#bigoasymptotic)
    -  [Amortized Notation](#bigoamortized)
    -  [Log N Runtimes](#lognruntimes)
    -  [Recursive Runtimes](#recursiveruntimes)
    -  [Space Complexity](#bigospace)
*  [Primitive Types](#primitivetypes)
    -  [Bitwise Operators](#bitwiseoperators)
*  [Arrays and Strings](#arraysandstrings)
    -  [Hashing](#hashing)
        +  [Hash Table](#hashtable)
        +  [Hash Function](#hashfunction)
        +  [Hash Function Example](#hashexample)
    -  [Hash Collision](#hashcollision)
        +  [Minimizing Hash Collisions](#hashminimizecollision)
        +  [Hash Folding](#hashfolding)
        +  [Hash Mid Square Method](#hashmidsquare)
        +  [Minimize Hashing Collisions with Characters](#hashchars)
    -  [Hash Collision Resolution](#hashcollisionresolution)
        +  [Hash Collision Resolution with Open Addressing](#hashopenaddressing)
        +  [Hash Collision Resolution with Open Addressing and Linear Probing](#hashlinearprobe)
        +  [Hash Collision Resolution with Open Addressing and Quadriatic Probing](#hashquadriaticprobe)
        +  [Hash Collision Resolution with Separate Chaining](#hashseparatechaining)
    -  [Array Resizing](#arrayresize)
*  [Lists](#lists)
    -  [Tuples](#tuples)
    -  [Sets](#sets)
*  [Linked Lists](#linkedlists)
    -  [Create a linked list](#)
    -  [Delete a Node from a Singly Linked List](#)
    -  [The 'Runner' Technique](#)
    -  [Recursive Problems](#)
    -  [Singly Linked Lists](#singlylinkedlists)
    -  [Doubly Linked Lists](#doublylinkedlists)
    -  [XOR Linked Lists](#xorlinkedlists)
    -  [Circular Linked Lists](#circularlinkedlists)
*  [Stacks and Queues](#stacksqueues)
    -  [Implement a Stack](#)
    -  [Implement a Queue](#)
    -  [Priority Queue](#priorityqueues)
    -  [Deque](#deque)
*  [Algorithm Design](#algorithmdesign)
    - [Incremental](#incremental)
        + [mathmatical induction](#mathmaticalinduction)
        + [loop invariant](#loopinvariant)
    - [Divide and Conquer](#divideandconquer)
        + [recursion](#recursion)
*  [Sorting Algorithms using Comparison Sort](#comparisonsortalgorithms)
    - [Incremental: insertion sort](#insertionsort)
    - [Incremental: bubble sort](#bubblesort)
    - [Divide and Conquer: merge sort](#mergesort)
    - [Divide and Conquer: quick sort](#quicksort)
*  [Sorting Algorithms using Decision-Tree Models](#decisiontreemodels)
    - [Counting Sort]
    - [Radix Sort]
    - [Bucket Sort]
*  [Growth of Functions](#growthfunctions)
    - [Big O Notation](#bigonotation)
*  [Probabilistic Analysis](#probabilisticanalysis)
    - [The Hiring Problem](#hiringproblem)
    - [The Birthday Paradox](#birthdayparadox)
    - [Balls and Bins](#ballsbins)
    - [Streaks (coin flips)](#streaks)
*  [Graph Theory](#graphtheory)
    - [Trees](#trees)
    - [Graphs](#graphs)
*  [Trees](#trees)
    - [Binary Trees](#binarytrees)
    - [Binary Search Trees](#binarysearchtrees)
*  [Heaps](#heaps)
    - [Max Heaps](#maxheaps)
    - [Min Heaps](#minheaps)
*  [Graphs](#graphs)


TODO:

*  [Graph Theory](#graphtheory)
*  [Greedy Methods](#greedy)
*  [Dynamic Programming](#dynamicprogramming)
*  [NP complete](#npcomplete)
*  [Parallelism](#parallelism)


# DATA STRUCTURES

##<a id="summary">Summary</a>

In computer science, a data structure is a data organization,
management and storage format that enables efficient access
and modification. A data structure is a collection of data
values, the relationships among them, and the functions or
operations that can be applied to the data.

####<a id="datastructure">Structure of Data</a>

An individual data piece (i.e. a __record__) is usually part of a bigger
collection of data.  Each record has a __key__ (e.g. a value to be sorted)
and the rest of the record has __satellite data__ (data carried around
with the key).

So what goes on behind the scenes?  When we move data around, we apply
a sorting algorithm; usually there's a large amount of satellite data
that makes it slow to physically move (e.g. erase from hard drive,
write to another place in hard drive) so instead we normally just move
our pointer to the records.

A __sorting algorithm__ is the method that we determine the sorted order
(regardless of individual fields or large satellite data). There's various
algorithms we can apply and various ways we can store our data.

####<a id="abstractdatatype">Abstract Data Type (ADT)</a>

An __abstract data type__ is a mathematical model for data types,
where a data type is defined by its behavior from the point of view of
a _user_ of the data, specifically in terms of possible values, possible
operations on data of this type, and the behavior of these operations.

We call these 'abstract' because it gives an implementation independent
view, (hiding the details of the implementation). For example, we have
data types like `int`, `float`, `char` with the knowledge of what values
can be assigned and what operations can be performed, but not know the
implementation details.

The issue with Python compared to other programming languages is that in say
Java, the naming scheme of a standard data structure is pretty straightforward.
In Java, there isn't just a `list`; it's either a `LinkedList` or an `ArrayList`.
In Python, we have a more 'human' naming scheme, meaning we just call it a `list`,
but really don't know if this is a linked list or a dynamic array.

What?

So let's try to break this down a bit. Say we swapped out 'data type' with
vehicle. What would be an 'abstract vehicle'? You would know what some of the
actions you could do with it would be (e.g. drive the vehicle, sit in the vehicle),
but it doesn't say exactly how you would do that (whether that's an automatic 
or manual vehicle, whether you need to open the door because its in a car or motorcycle).
Some of the data stored might be say the color of the vehicle, number of wheels, etc.
The idea is that we get an implmentation independent view. We hide the details of 
so that it's an __abstraction__. We don't care about details like the number of wheels,
the number of doors (if any) or how to apply an action (e.g. drive stick or automatic); 
it's all a black box. We do this to create an __encapsulation__ around the data.

So we might have an Abstract Data Type like an Integer (e.g. -2, -1, 0, 1, 2) where
we can do operations like addition, subtraction, multiplication, division, and comparisons
like equals, greater than, less than. We don't care how the computer moves the data
around, whether it's a binary number or a binary-coded decimal. We simply say 'hey, add
these two numbers together and give me the result'.

Some common abstract data types include:

* Container
* List
* Set
* Multiset
* Map
* Multimap
* Graph
* Tree
* Stack
* Queue
* Priority Queue
* Double-ended queue
* Double-ended priority queue

User vs Implementor

Imagine if you're the __user__ that has to interact with the interface, using the
operations that have been specified by the abstract data type. You might call a `get_max`
method or a `delete_last` method, but not be concerned about how its implemented, since
there can be many different ways to implement an abstract data type; i.e. we create
an __implementation-indepdent__ view of the data.

The implementation of an abstract data type is often called the __data structure__.

Programming Specific

In a programming language like Python, we have objects like Abstract Base Class.
Here we see what methods are available, but they're not yet implemented.


#####<a id="fundamentalabstractdatatype">Fundamental Abstract Data Type (ADT)</a>

There are a few fundamental abstract data types:

* Containers
* Dictionaries
* Binary Search Trees
* Priority Queues

Containers

__Containers__ are _abstract data types_ that hold stuff. We use the container to
hold data and retrieve data items independently of content. Some popular types of
containers are:

* Stacks
* Queues
* Tables (implemented through arrays)

Dictionaries

__Dictionaries__ are a form of container that gives you access to data (values) by keys.

Binary Search Trees

__Binary Search Trees__ is a binary tree where each node contains at most two children. 
Each child can be identified as either a left or right child.

Priority Queues

__Priority Queues__ are a list with a priority (i.e. sorting items inside).
Priority Queue operations can be implemented in time by representing the heap with a
binary search tree.

####<a id="adtandclasses">Abstract Data Types and Classes</a>

So how does an __Abstract Data Type__ work with a programming language's __Class__?
We define a class to be a description of what:

* the data looks like (i.e. the state)
* what the data can do (i.e. the behavior)

Data items are called __objects__ in this object-oriented paradigm. An object is an instance
of a class.

Python specific Classes

Python has a number of built-in classes, from:

* Boolean Classes (e.g. i.e. True, False)
* Numeric Classes (e.g. int, float)
* Ordered Collection Classes (e.g. lists, strings, tuples)
* Unordered Collection Classes (e.g. sets, dictionaries)

####<a id="listfordatastructures">Using Lists to build data structures</a>

A lot of data structures can be implemented with the help of arrays and lists.
In Python, arrays are lists and lists are say custom classes for:

* single linked list (sorted)
* single linked list (unsorted)
* double linked list (sorted)
* double linked list (unsorted)

You might see the following methods for the data structure of `alist`.

Operations include:

* indexing - `[]` to access an element of a sequence
* concatentation - `+` to combine sequences together
* repetition - `*` to concatenate a repeated number of times (good for initializing a list)
* membership - `in` to check if an item is in a sequence
* length - `len` to return the number of items in the sequence
* slicing - `[:]` to extract a part of a sequence (up to, but not including)

Methods include:

* `append` - `alist.append(item)` - add a new item to the end of a list
* `insert` - `alist.insert(i, item)` - insert an item at the `i`th position in a list
* `pop` - `alist.pop()` to remove and return the last item in a list
* `pop` - `alist.pop(i)` to remove and return the `i`th item in a list
* `sort` - `alist.sort()` to modify a list to be sorted (modifies inplace)
* `reverse` - `alist.reverse()` to modify a list to be in reversed order
* `del` - `del alist[i]` to delete the item in ith position (modifies inplace)
* `index` - `alist.index(item)` - returns the index of the first occurrence of item
* `count` - `alist.count(item)` - returns the number of occurrences of item
* `remove` - `alist.remove(item)` - removes the first occurrence of item

####<a` id="stringfordatastructures">Using Strings to build data structures</a>

You might see the following methods for the data structure of `astring`.

Methods include:

* `center` - `astring.center(w)` - returns a string as size `w` that is center-justified (e.g. `   hey there   `)
* `count` - `astring.count(item)` - returns the number of occurrences of `item` in the string
* `ljust` - `astring.ljust(w)` - returns a string as size `w` that is left-justified
* `lower` - `astring.lower()` - returns a string in all lowercase
* `rjust` - `astring.rjust(w)` - returns a string as size `w` that is right-justified
* `find` - `astring.find(item)` - returns the index of the first occurrence of `item`
* `split` - `astring.split(schar)` - splits a string into substrings at `schar`

####<a` id="setsfordatastructures">Using Sets to build data structures</a>

Use Sets with:

    engineers = set(['John', 'Jane', 'Jack', 'Janice'])

Operations include:

* `in` - checks for set membership (e.g. `'John' in set(['John', 'Jane'])`)
* `len` - returns the cardinality of the set (e.g .`len(set(['John', 'John', 'Jane']))`)
* `|` - `aset | otherset` returns a new set with all elements from both sets
* `&` - `aset & otherset` returns a new set with only those elements common to both sets
* `-` - `aset - otherset` returns a new set with all items from the first set not in the second set
* `<=` - `aset <= otherset` asks Boolean whether all elements of the first set are in the second set

Methods include:

* `union` - `aset.union(otherset)` - returns a new set with all elements from both sets
* `intersection` - `aset.intersection(otherset)` - returns a new set with only those elements common to both sets
* `difference` - `aset.difference(otherset)` - returns a new set with all items from first set, not otherset
* `issubset` - `aset.issubset(otherwise)` - Asks whether all elements of one set are in the other set
* `add` - `aset.add(item)` - adds item to the set (inplace)
* `remove` - `aset.remove(item)` - removes item to the set (inplace)
* `pop` - `aset.pop()` - remove an arbitrary element from the set
* `clear` - `aset.clear()` - removes all elements from the set

####<a` id="dictsfordatastructures">Using Dictionaries to build data structures</a>

Operators include:

* `[]` - `myDict[k]` returns the value for key `k`, otherwise an error
* `in` - `mykey in mydict` - returns True if key is in the dictionary, otherwise False
* `del` - `del mydict[key]` - removes the entry from the dictionary

Methods include:

* `keys` - `mydict.keys()` - returns the keys of the dictionary in a `dict_keys` object
* `values` - `mydict.values()` - returns the values of the dictionary in a `dict_values` object
* `items` - `mydict.items()` - returns the key-value pairs in a `dict_items` object
* `get` - `mydict.get(mykey)` - returns the value associated with key 'mykey', `None` otherwise
* `get` - `mydict.get(mykey, altvalue)` - returns the value associated with key 'mykey', 'altvalue' otherwise

#####<a id="datamodeling">Modeling a Problem</a>

Modeling is the art of formulating your application in terms of precisely
described, well understood problems. Modeling is the key to applying algorithmic
design techniques to real-world problems. It's a way of relating your application
to what has been done before.

Most algorithms are designed to work on a rigorously defined __abstract__ structure,
such as a permutation or graph or set. To utilize algorithms to their fullest
extent, you have to be able to describe your problem abstractly in terms of
procedures on fundamental structures.

What this means is that if you're in a company building software for "widgets",
you can't look up a solution to a "widget optimization problem".
Instead, you have to describe how your widget fits into common data structures
(to then leverage the common methods of those data structures).

#####<a id="fundamentaldatastructures">Fundamental Data Structures</a>

So with your widget, you can see if it fits any of these fundamental data structures:

* __Permutations__ - an arrangement, or ordering, of items. For example,
  {1, 2, 3, 4} and {3, 2, 4, 1} are two distinct permutations of the same
  set of numbers. Note that ordering matters. Usually in questions that 
  look for an "arrangement", "tour", "ordering" or "sequence"
* __Subsets__ - represents selections from a set of items. For example,
  {1, 3, 4} and {2} are two distinct subsets of the first four integers.
  Order does not matter in subsets. {1, 3, 4} is identical to {3, 4, 1}.
  Usually in questions that look for a "cluster", "collection", "committee",
  "group", "packaging", or "selection"
* __Trees__ - represents hierarchical relationships between items. Trees
  are usually in questions seeking a "hierarchy", "dominance relationship",
  "ancestor/descendant relationship" or "taxonomy"
* __Graphs__ - represents relationships between arbitrary pairs of objects.
  An example is a network of roads as a graph, where the vertices are cities
  and the edges are roads between connecting pairs of cities. Usually in
  questions that look for "network", "circuit", "web", or "relationship"
* __Points__ - represents locations in some geometric space. For example,
  the location of a restaurant or house on a map. Usually in questions that
  look for "sites", "positions", "data records", or "locations"
* __Polygons__ - represents regions in a geometric space. For example,
  the borders of a county or state as shown on a map. Polygons and polyhedra
  are likely in questions working on "shapes", "regions", "configurations",
  or "boundaries".
* __Strings__ - represents sequences of characters or patterns. For example,
  the names of students in a class. Strings are likely in questions dealing
  with "text", "characters", "patterns", or "labels"

These fundamental data structures are important because they provide the
language to use to model applications. Study the _input_ and _output_ of each
problem and you'll know where to look when the problem arises in your application.

Modeling is only the first step in designing an algorithm for a problem,
but its also the most important step towards a solution. Make sure you
model the problem correctly before trying to solve it.

###<a id="contiguouslinked">Contiguous vs Linked Data Structures</a>

Data structures can be either __contiguous__ or __linked__, depending
on whether they are based on arrays or pointers.

* __Contiguously-allocated structures__ are single slabs of memory;
  these include arrays, matrices, heaps, and hash tables
* __Linked data structures__ are distinct chunks of memory bound
  together by _pointers_, and include lists, trees, and graph adjacency
  lists

####<a id="comparisonarraylinked">Comparison: Linked Lists versus static Arrays</a>

Advantages of Linked List over Arrays

* Overflow on a linked list can never occur unless the memory is actually full
* Insertions and deletions are simpler on linked lists than a contiguous arrays
* With large records, moving pointers is easier and faster than moving the items themselves

Advantages of Arrays over Linked Lists

* Linked structures require more space for storing pointer fields (whereas arrays just store the data)
* Linked lists do not allow efficient random access to items
* Arrays allow better memory locality and cache performance than random pointer jumping

##<a id="whatisalgorithm">What is an Algorithm?</a>

An __algorithm__ is a procedure to accomplish a specific task.
An algorithm must solve a general, well-specified _problem_.
An algorithmic problem is specified by describing the complete set of
_instances_ it must work on and of its output after running on one of
these instances.

We take an __input__ (i.e. some value(s)) and produces an __output__
(some value(s)).  For example, we might encounter a _sorting problem_
where we want to sort a sequence of numbers into nondecreasing order.

An _instance_ of sorting might be an array of names like `['Mike', 'Bob',
'Sally', 'Jill', 'Jan']` whereas an algorithm takes any of the possible
input instances and transforms it to the desired output.

###<a id="expressingalgorithms">Expressing Algorithms</a>

You can express algorithms in three common forms:

* English
* pseudocode
* a real programming language

The best notation depends on which method you are most comfortable with.
Usually I prefer to express the _ideas_ of an algorithm in English, then
move to a more formal programming language like Python. If your idea
is not clearly revealed when you express an algorithm, then you are using
too low-level a notation to describe it.

###<a id="whatareyousolving">What's the problem?</a>

Before even looking at an algorithm, ask yourself if you have a careful 
description of the problem that the algorithm is trying to solve? Problem
specifications usually have two parts:

1. The set of allowed input instances
2. The required properties of the algorithm's output

It is impossible to prove the correctness of an algorithm for a fuzzily-stated problem.
Ask the wrong problem and you will get the wrong answer.

Its very important that you try to narrow the set of allowable instances
until there is a correct and efficient algorithm.

For example, what is the best route between these two places? We need to
define what 'best' means, whether that is shortest in distance, time, or
minimizing the number of turns.

###a id="thinkrecursively">Recursive Objects</a>

When you're trying to find what the problem is, learning to think recursively 
is to important so that you know how to look for big things that
are made from smaller things that are made exactly the same type as the big thing.
Think of houses as sets of rooms, then adding or deleting a room and you still
have a house behind. If we look at our fundamental data structures, we have:

* Permutations - delete the first element of a permutation {1, ..., n} and you get a
  permutation of the remaining `n-1` things.
* Subsets - every subset of the elements {1, ..., n} contains a subset of {1, ...., n-1}
  by deleting element n
...

These recursive descriptions of objects require both decomposition rules and _basis cases_,
namely the specification of the smallest and simplest objects where the decomposition stops.

###<a id="agoodalgorithm">What makes a good algorithm?</a>

In a good algorithm, you want to look for:

1. Is the algorithm correct?
2. Is the algorithm efficient?
3. Is the algorithm easy to implement?

Sometimes you can't achieve all three.

####<a id="algorithmcorrect">Is the algorithm correct?</a>

An algorithm is correct if _every_ input instance halts with the correct
output.  A single input is an __instance of a problem__.  For example:

    // an instance of a problem Input Sequence of {31, 41, 59, 26, 41, 58}
    // Output Sequence of {26, 31, 41, 41, 58, 59}

It is usually not obvious whether a given algorithm correctly solves a given problem.

Correct algorithms usually come with a __proof of correctness__, which is
an explanation of why we know that the algorithm must take every instance of
the problem to the desired result. We can't just say "it's obvious", because usually
it's not obvious.

There's a fundamental difference between __algorithms__, which always produce a
correct result, and __heuristics__, which may usually do a good job, but without
providing any guarantee.

#####<a id="disprovealgorithms>Disproving algorithms</a>

Searching for __counterexamples__ is the best way to disprove the correctness of a heuristic.

Good counterexamples have all unnecessary details boiled away. Make it clear
why the proposed algorithm fails. Try to:

* think small - when algorithms fail, there is usually a very simple example that fails
* think exhaustively - think of the types of possible answers
* hunt for the weakness - if an algorithm "always takes the biggest" (aka greedy), think
  of weaknesses like "what is there is a tie" or mix extremes (e.g. tiny and huge, near and far)
* seek extremes - it is easier to verify or reason about extreme examples
  than more muddled ones

#####<a id="provealgorithms>Proving algorithms</a>

A proof or demonstration of correctness is needed. Usually this is done
through __mathematical induction__. So what is it? Think of mathematical induction
like recursion. You break a problem up by solving a base case and then any
smaller pieces.

####<a id="algorithmefficient">Is the algorithm efficient?</a>

There are different algorithms to solve the same problem and they are
often drastically different in efficiency.  We normally measure efficiency
by looking at __running time__, which is mainly affected by the __size
of input__.  However, Big O notation is also used to check space complexity. 
See __Big O__ for further details.

1. the __size of input__ normally means looking at the _number of items
in the input_; for other problems like multiplying integers, we look at
the _total number of bits_ used.
2. the __running time__ is the number of 'steps' (i.e. the number of
times something is executed).

####<a id="algorithmstable">Is a sorting algorithm stable?</a>

For sorting algorithms, you might hear that one type is 'stable' while
others are 'unstable'.

* A __stable sorting algorithm__ keeps the original order of the input set,
which only matters when two elements are equal (say sorting cards by
value, but we have two 5's, one of hearts and one of diamond).
* An __unstable sorting algorithm__ does not keep the original order of the input
set, so if you sorted the same cards from above, the order might not
be preserved (e.g. 5 of diamonds comes before 5 of hearts)
* Things end up being a tradeoff, with stable sorting usually being
less efficient, but sometimes you need the original order preserved.

##<a id="walkthrough">Walking through an Algorithm Problem</a>

1. Listen Carefully
2. Draw an Example
3. State a Brute Force
4. Optimize
5. Walk Through
6. Implement

###<a id="walkthroughlisten">Listen Carefully</a>

Mentally record any _unique_ information in the problem. For example:

* "Given two arrays that are sorted, find..."
    - Because the data is sorted, the optimal algorithm is different
      than in an unsorted situation
* "Design an algorithm to run repeatedly on a server that..."
    - Run repreatedly is different than run once. Maybe you have to
      cache your data or you can do more precomputation on the initial
      dataset?

Ask yourself if you've used all the information in the problem,
especially if you get stuck.

###<a id="walkthroughexample">Draw an Example</a>

Do not skip this step. Draw a good example, meaning watch out to:

* Make an example that is not too small, not too large (most tend to be too small)
* Needs to be specific - use real numbers or strings
* Make sure its not a special case

Make sure you really understand what you're trying to solve.
You can't come up with the right solution if you're solving the wrong problem!

Your example should help reinforce your understanding of the problem and
whether any algorithms that you apply will work.

###<a id="walkthroughbruteforce">State a Brute Force</a>

After the example, state a brute force answer (even if it's obvious to you).
It's okay that the initail solution is terrible. Explain the space and time
complexity, then improve it. It's a great starting point for optimizations
and it helps you wrap your head around the problem.

###<a id="walkthroughoptimize">Optimize</a>

After the brute force algorithm, you'll need to come up with an optimized algorithm.

* Look for unused information (e.g. if an array was sorted)
* Use a fresh example - sometimes a new example will unclog your mind or help
  you see a pattern in the problem
* Solve it "incorrectly" to see if that gives you any insights to finding the correct solution
* Make time vs space tradeoffs. Sometimes storing extra state can help optimize the runtime
* Precompute information - e.g. can you reorganize the data (i.e. sort) or compute some
  values upfront that will help save time in the long run?
* Use a hash table - these are very common
* Think about the best conceivable runtime

###<a id="walkthrough">Walk Through</a>

After you've gotten an optimal algorithm, don't dive into coding. Take a moment
to solidify your understanding of the algorithm.

Whiteboard coding is very slow; same with testing and fixing code. Make sure
you have code that is close to perfect in the beginning.

If you don't understand exactly what you're about to write, you'll struggle to code it.
It will take longer to finish the code and you'll make major errors.

###<a id="walkthroughimplement">Implement</a>

When you have an optimal algorithm and know exactly what you're going to write, then
go ahead and implement it. Start coding on the top left corner, avoid "line creep"
(where each line of code is at a slant) and remmber spacing.

Write beautiful code, meaning:

* __Modularized code__ - e.g. have function `init_matrix(size)` instead of filling in details
* __Error checks__ - Add tests or at least add a TODO
* Use other classes/structs where appropriate (e.g. a list of start and end points could be
  a two-dimensional array, but probably better as a single list of objects as Class `StartEndPair`)
  You can fill in the details for that new Class later and will allow you to expand on that dataset.
* Choose good variable names, not single variable names

###<a id="walkthroughtest">Test</a>

Don't submit code in an interview without testing it.

* Start with a 'conceptual' test - meaning just read and analyze what each line of code does.
  Does the code do what you think it should do?
* Weird looking code - double check that weird code
* Hot spots - Check for the base case in recursive code, integer division, null nodes in binary trees,
  the start and end of iteration through linked list, etc.
* Small test cases - Use small test cases like an array of 3 or 4 elements. You'll find the same bugs, but faster
* Special cases - Test your code against null or single element values, extreme cases

When you find bugs, don't just make the first correction you can think of. Analyze why the bug
occurred and ensure your fix is the best one.

##<a id="bud">Optimize and Solve Technique 1: BUD</a>

Look for __BUD__:

* Bottlenecks
* Unnecessary work
* Duplicated work

###<a id="bottlenecks">Bottlenecks</a>

A __bottleneck__ is a part of your algorithm that slows down the overall runtime. This can occur
when:

* You have one-time work that slows down your algorithm
* You have a chunk of work that's done repeatedly, like searching

###<a id="unnecessarywork">Unnecessary Work</a>

###<a id="duplicatedwork">Duplicated Work</a>

##<a id="diy">Optimize and Solve Technique 2: DIY</a>

Try to solve the problem manually with a real life example, like instead 
of "Create a sorting algorithm" using binary search, we think about how to 
locate a student's paper.

##<a id="simplifygeneralize">Optimize and Solve Technique 3: Simplify and Generalize</a>

We'll implemnet a multi-step approach:

* First, simplify or tweak some constraint, such as the data type
* Then solve this new simplified version of the problem

##<a id="basecaseandbuild">Optimize and Solve Technique 4: Base Case and Build</a>

We solve the problem first for a base case (e.g. `n=1`) and then try to build up
from there. When we get to more complex cases (e.g. `n=3`, `n=4`), we try to build
those using the prior solutions. Now that we understand the pattern, try to use
say a recursive algorithm.

##<a id="datastructurebrainstorm">Optimize and Solve Technique 5: Data Structure Brainstorm</a>

Run through the list of data structures and try to apply each one.
This might help if you're stuck on a problem and realize that if you used say a tree, it's a trivial problem.

Use data structures generously!!!

##<a id="bcr">Best Conceivable Runtime (BCR)</a>

Consider the best conceivable runtime and what that solution might look like in Big O.

##<a id="goodcode">Good Code</a>

Try to write good code, meaning:

* Correct - code operates correctly on all expected and unexpected inputs
* Efficient - code operates efficiently as possible in terms of both time and space
* Simple - code should be simple and be as quick to write as possible
* Readable - a different developer should be able to read your code and understand what it does
* Maintainable - code should be reasonably adaptable to changes during the life cyle of a product

##<a id="bigo">Big O</a>

We use __Big O notation__ to give an estimated running time based on the
input size (as it tends toward infinity).  Big O is basically a math
formula that counts how many steps of computations get executed given
a specified input size; the more computational steps the slower it is.
We characterize these functions according to their __rate of growth__
(aka __growth rate__), which is represented using the __O Notation__. We
also care about space (so see Space Complexity below).

Note: We also use Big O for measuring space complexity (not just time)!

###<a id="bigooperations">Big O Operations</a>

When we say calculate Big O, we often have some scenarios we want to look at:

* Time Complexity vs Space Complexity
* Average vs Worst
* For Data Structures: Access, Search, Insertion, Deletion
* For Sorting: Best, Average, Worst

Note: Access vs Search - Access is like if I knew your mailbox, I would go there directly. Search would be I know
      your mailbox is somewhere in this block, start searching through.
      You might not always have a constant Access (e.g. linked list might take you through multiple items before
      you find the one you're looking for)

####<a id="bigoexample">Big O Example</a>

Say you want to get a hard drive of information to your friend across
the country.  We can do an 'electronic transfer' through email or a
'physical transfer' by flying out there with the hard drive.  Each method
has a different time complexity:

*  'Electronic Transfer' can be described as `O(s)` where `s` is the
size of the file (for this example we say this is a linear function,
but it can have a different __rate of growth__) *  'Airplane Transfer'
can be described as `O(1)` where `1` is a constant that simply means as
the data gets larger, it doesn't affect our flight time.

If we compare these two scenarios, the 'Airplane Transfer' time is slower
for small files, but at some point beats out the 'Electronic Transfer'.
This general idea is that the _running time of an algorithm is a function
of the size of its inputs_.

###<a id="algorithmanalysistools">Algorithm Analysis Tools</a>

Big O is used to compare the efficiency of algorithms without implementing them.
We have a couple tools for this:

1.) RAM model of computation
2.) Asymptotic analysis of worst-case complexity

####<a id="rammodel">RAM Model of Computation</a>

Machine-independent algorithm design depends upon a hypothetical computer
called the _Random Access Machine_ (RAM). This computer strikes a balance
of capturing essential behavior of computers while being simple to work with.

* Each simple operation (`+`, `*`, `-` etc) takes exactly one time step 
* Loops and subroutines are not considered simple operations
* Each memory access takes exactly one time step
* We have unlimited memory
* There is no notice if an item is in cache or on disk

We measure _run time_ by counting up the number of steps an algorithm takes
on a given problem. Our imaginary machine runs a given number of steps per second.

With RAM, we can count how many steps our algorithm takes on any given input instance
by executing it. We look at the best-case, average-case, and worst-case complexity.
Usually we use the worst-case complexity.

####<a id="bigocommon">Common O Notations</a>

Common __O Notation__ runtimes are below (from the fastest first to
slowest last):

*  `O(1)` - __constant__; e.g. check if a number is even or odd (uses
    a constant-size lookup or hash table)
*  `O(log N)` - __logarithmic__; e.g. find an item in a sorted array
    with a binary search (because we split in half)
*  `O(N)` - __linear__; e.g. find an item in an unsorted list
*  `O(N log N)` - __loglinear__; e.g. heapsort, quicksort (best and avg case),
    merge sort
*  `O(N^2)` - __quadratic__; e.g. selection sort, insertion sort,
    worst case for bubble sort, quicksort (worst case)
*  `O(2^N)` - __exponential__; e.g. finding exact solution to traveling salesman
    problem using dynamic programming
*  `O(N!)` - __factorial__; e.g. solving traveling salesman problem via
    brute-force search

A good cheatsheet is [here](http://bigocheatsheet.com/ "Big O Cheatsheet")

####<a id="bigotime">Evaluating Runtimes</a>

When evaluating time, we can look at the __O Notation__ a few different
ways.

*  __big omega__ looks at the __worst-case running time__; this is the norm
*  __big theta__ looks at the __average-case running time__; this
   is usually used for _probabilistic analysis_.
*  __big O (aka O)__ looks at the __best-case running time__; not usually used


####<a id="bigoasymptotic">Asymptotic Notation</a>

What really happens is that we're interested in the __asymptotic
notation__ runtime, which is how the algorithm runtime scales for
very large inputs and not in the minute details or small inputs.
This means we:

*  Drop the Constants (e.g. `O(2N)` is actually `O(N)`)
*  Drop the Non-Dominant Terms (e.g. `O(N^2 + N^2)` is actually `O(N^2)`)
    -  E.g. `O(N + log N)` becomes `O(N)` -  E.g. `O(N^2 + N)` becomes `O(N^2)`
*  Keep the terms that grow bigger when `N` approaches `infinity`

__Add or Multiple__

If we do code chunk A completely then start code chunk B, the notation
should look like `O(A + B)`.  If we do code chunk A and code chunk B is
in code A (e.g. for-loop in a for-loop), the notation should look like
`O(A * B)`.

####<a id="bigoamortized">Amortized Time</a>

__Amortized time__ is like doing an operation say a million times.
You don't care about the worse-case or best-case scenario every once in
a while.  We only care about the time taken in total to do the million
operations.

####<a id="lognruntimes">Log N Runtimes</a>

`O(log N)` is a common runtime because of the __elements being halved__
scenario.  For example, in a binary search for `x` in an N-element sorted
array, the options are:

1. if `x==middle` then return middle 2. if `x<middle` then search left
3. if `x>middle` then search right

The runtime for this looks like this (say given an N-element array of 16):

1. N=16 # divide by 2
2. N=8  # divide by 2
3. N=4  # divide by 2 4. N=2
# divide by 2 5. N=1  # divide by 2

We could reverse this to say how many times can we multiply by 2 until
we get N?

1. N=1  # multiply by 2
2. N=2  # multiply by 2
3. N=4  # multiply by 2
4. N=8  # multiply by 2
5. N=16 # multiply by 2

We then get `2^k=N`, which is what `log` expresses.  For example:

*  `2^4=16`, which is `logBase(16,2)=4`  where `logBase(value, base)`
* `logBase(N,2)=k`, which is `2^k=N`

####<a id="bigorecursive">Recursive Runtimes</a>

When you have a recursive functions that makes multiple calls, the
runtimes will often (not always) look like `O(branches^depth)` where
'branches' is the number of times each recursive call branches.

####<a id="bigospace">Space Complexity</a>

Besides time, other considerations in real life (that we won't consider
for now) are things like space (RAM, hard drive), bandwidth speed,
caches, and parallelism (single, multiple cores).

The general rule is if we need to create an array of size `n`, we would
require `O(n)` space.

##<a id="primitivetypes">Primitive Types</a>

A program updates variables in memory according to code. Variables
come in __types__, a classification of data saying what the possible
values are and what operations can be performed on it (e.g. add a
number, check the length of a string). In Python, everything is
an object, including different types like:

* __numerics__ (Boolean, integers)
* __sequences__ (lists)
* __mappings__ (dict)
* __classes__
* __instances__
* __exceptions__

###<a id="bitsbytes">Bits and Bytes</a>

A __bit__ is atomic, the smallest unit of storage, storing just a `0` or `1`
If we have just one bit, the pattern can be either `0` or `1`.
If we have two bits, we can have patterns like `00`, `01`, `10`, `11`
If we have three bits, we can have patterns like `000`, `001`, `010`, etc.
With each additional bit, we can double the number of previous patterns

    1 bit = 2 patterns
    2 bits = 4 patterns
    3 bits = 8 patterns
    4 bits = 16 patterns
    5 bits = 32 patterns
    6 bits = 64 patterns
    7 bits = 128 patterns
    8 bits = 256 patterns (one byte)

Mathematically: n bits yields `2^n` patterns

A __byte__ is a collection of 8 bits. One byte can store one character (e.g. 'A', '$')

    Kilobyte (KB) = 1 thousand bytes
    Megabyte (MB) = 1 million bytes
    Gigabyte (GB) = 1 billion bytes
    Terabyte (TB) = 1 trillion bytes

Examples of ASCII

    A is 65
    B is 66
    space is 32

###<a id="bitwiseoperators">Bitwise Operators</a>

__Bitwise operators__ act on operands as if they were a string of binary
digits. It operates bit by bit, meaning we have `O(1)` computations per
bit and a time complexity of `O(n)`. Common bitwise operators in Python are:

    &  # bitwise AND, e.g. x& y =0      (0000 0000)
    |  # bitwise OR, e.g. x | y = 14    (0000 1110)
    ~  # bitwise NOT, e.g. ~x = -11     (0000 0101)
    ^  # bitwise XOR, e.g. x ^ y = 14   (0000 1110)
    >> # bitwise right shift, x>> 2 = 2 (0000 0010)
    << # bitwise left shift, x<< 2 = 40 (0010 1000)

So what exactly does a bitwise operator do?

* AND is 1 only if both of its inputs are 1, otherwise it's 0.
* OR is 1 if one or both of its inputs are 1, otherwise it's 0.
* XOR is 1 only if exactly one of its inputs are 1, otherwise it's 0.
* NOT / Ones Compliment is 1 only if its input is 0, otherwise it's 0.

These can appear as a truth table:

    AND | 0 1     OR | 0 1     XOR | 0 1    NOT | 0 1
    ----+-----    ---+----     ----+----    ----+----
     0  | 0 0      0 | 0 1       0 | 0 1        | 1 0
     1  | 0 1      1 | 1 1       1 | 1 0

An example would be if you only wanted the lower 4 bits of an integer, you `AND`
it with 15 (binary `1111`). The zero bits act as a filter, forcing the bits in
the result to be zero as well.

        201: 1100 1001
    AND  15: 0000 1111
    ------------------
     IS   9  0000 1001

In addition, >> and << are often included as bitwise operators, and they
"shift" a value respectively right and left by a certain number of bits,
throwing away bits that roll of the end you're shifting towards, and
feeding in zero bits at the other end.

    1001 0101 >> 2 gives 0010 0101
    1111 1111 << 4 gives 1111 0000

Note that with Python, a left shift simply expands the width of the
number for the extra bits (whereas other languages drop off the extra bits).

So why is this important? Think of when you `chmod` a file to give permissions.
Each file and folder has 8-bit data that controls the permissions.

* Read is equivalent to ‘4’.
* Write is equivalent to ‘2’.
* Execute is equivalent to ‘1’

`000` means no permissions. To read and write it would be `6 (4+2)`. To read, write
and execute it would be `7 (4+2+1)`. Common permissions are:

* `755` common in web servers; owner has all permissions, everyone else read and execute,
  but not make changes to a file
* `777` everyone can do everything
* `644` only the owner can read and write, everyone else can only read. No one can execute
* `655` only the owner can read and write, but not execute the file. Everyone else can
  read and execute, but cannot modify the file.

###<a id="numericmethods">Numeric Methods</a>

Key methods for numeric types are:

* `abs(-35)`
* `math.ceil(2.17)`
* `math.floor(3.14)`
* `min(x, -4)`  # returns the minimum number out of these
* `max(3.14, y)`  # returns the maximum number out of these
* `pow(2.71, 3.14)`  # same as `2.71 ** 3.14`
* `math.sqrt(225)`
* float(`inf`)  # floats are not inifnite precision, use this to refer to infinity
* float(`-inf`)  # floats are not inifnite precision, use this to refer to negative infinity
* `math.isclose()`  # for comparing floating point values, returns True or False
* random.randrange(5, 10, 2)`  # e.g. returns 7, random number from 5 to 10 with increments of 2

It's good to know what the max size is on your machine using `sys.maxsize` and `sys.float_info`

    +>>> import sys
    +>>> sys.maxsize
    9223372036854775807
    +>>> sys.float_info
    sys.float_info(max=1.7976931348623157e+308, max_exp=1024, max_10_exp=308, min=2.2250738585072014e-308,
    min_exp=-1021, min_10_exp=-307, dig=15, mant_dig=53, epsilon=2.220446049250313e-16, radix=2, rounds=1)

###<a id="computingparity">Computing the Pairity of a Word</a>

The pairity of a binary word is 1 if the number of 1's in the word is odd. Otherwise, it is 0.
Parity checks are used to detect single bit errors in data storage and communication.

    x & (x-1)  # equals x with its lowest set bit erased
    e.g. x = (00101100), then x - 1 = (00101011)
         x & (x-1) = (00101100) & (00101011) = (00101000)

        00101100  x
        00101011  x-1
        00101000  result

###<a id="builtintypes">Built-in Types</a>

Python has a few built-in types. This is for Python 3.6:

* Numeric Types - int, float, complex
* Iterator Types
* Sequence Types - list, tuple, range
* Text Sequence Type - str
* Binary Sequence Types - bytes, bytearray, memoryview
* Set Types - set, frozenset
* Mapping Types - dict

https://docs.python.org/3.6/library/index.html

##<a id="mustknow">Must Knows</a>

###<a id="mustknowdatastructures">Data Structures</a>

* Linked Lists
* Trees, Tries, & Graphs
* Stacks & Queues
* Heaps
* Vectors / ArrayLists
* Hash Tables

###<a id="mustknowalgorithms">Algorithms</a>

* Breadth-First Search
* Depth-First Search
* Binary Search
* Merge Sort
* Quick Sort

###<a id="mustknowconcepts">Concepts</a>

* Bit Manipulation
* Memory (Stack vs Heap)
* Recursion
* Dynamic Programming
* Big O Time & Space

###<a id="mustknowtable">Powers of 2 Table</a>

Use this table below for questions about scalability or memory limitation.
For example, if we have a bit vector mapping of every 32-bit integer to a boolean 
value, we need 2^32 bits (or 2^30 bytes) to store this mapping; that's approximately 
1 GB of memory. Remember that 1 bit = .125 bytes (8 bits = 1 byte)

    Power of 2  |   Exact Value (X)     |   Approx Value    |   X Bytes in MB, GB, TB
            7   |               128     |                   |
            8   |               256     |                   |
           10   |              1024     |   1 thousand      |           1 KB
           16   |             65536     |                   |          64 KB
           20   |           1048576     |    1 million      |           1 MB
           30   |        1073741824     |    1 billion      |           1 GB
           32   |        4294967296     |                   |           4 GB
           40   |     1099511627776     |    1 trillion     |           1 TB

##<a id="arraysandstrings">Arrays and Strings</a>

###<a id="arrays">Arrays</a>

The __array__ is the simplest data structure; it is a contiguous block of memory,
usually used to represent sequences. If we have an Array _A_, then we can say that
`A[i]` is the `(i + 1)th` object stored in the array (because `A[i]` starts at 0).
Retrieving and updating `A[i]` takes `O(1)` calculation time.

####<a id="arrayadvdisadv">Array Advantages and Disadvantages"</a>

Advantages of a contiguously-allocated arrays (i.e. a 'list' in Python) are:

* Constant-time access when given the index. The index of each element maps directly
  to a particular memory address so we can access arbitrary data items instantly
  if we know the index.
* Space efficient - Arrays are made of purely data; there's no links/pointers or
  any other information. We don't even need an 'end-of-record' info because
  arrays are built from fixed-size records
* Memory locality - Programming commonly requires iterating through all the elements
  of a data structure. Arrays are good for this because they have excellent memory
  locality, meaning that because the physical memory being accessed is so close to 
  each other, we can take advantage of the high-speed __cache memory__ on modern computers.

Disadvantages are:

* We cannot adjust the size of an array in the middle of a program's execution.
  If we allocate an extremely large array, this can waste space
 
We can efficiently enlarge arrays as we need them through __dynamic arrays__.
As soon as we need a larger array, we double the size and copy the contents of the
smaller array into the larger array. The primary thing lost using dynamic arrays
is the guarantee that each array access takes constant time in the worst case.
All queries will be fast, except those relatively few queries triggering array doubling.

####<a id="arraycrudoperations">Array CRUD Operations"</a>

The time for operations really depends on whether an array is sorted or unsorted.

####<a id="sortedarrays">Sorted Arrays</a>

Inserting and Deleting

For a sorted array, deleting an element from an array means moving all successive 
elements one over to the left to fill the vacated space. For example, if an array is:
`[2, 3, 5, 7, 9, 11, 13, 17]`, then deleting an element at index 4 results in
`[2, 3, 5, 7, 11, 13, 17, 0]`. The time complexity of deleting an element at
index `i` from an array of length `n` is `O(n-i)`. The same is true for inserting
a new element (as opposed to updating an existing entry). The reason for this is that
we now need to move elements over.

####<a id="arrayproblem1">Array Problem 1</a>

Say we have an input array consisting of integers. Your job is to reorder its entries
so that the even entries appear first. We can do this easily in `O(n)` space where `n`
is the length of the array. However, this is more difficult to solve without allocating
additional storage.

##<a id="hashingandstrings">Hashing and Strings</a>

__Hash tables__ are a very practical way to maintain a dictionary.
They exploit the fact that looking up an item in an array takes constant time
once you have its index. A __hash function__ is a mathematical function that
maps keys to integers. We use the value of our hash function as an index into
an array, and store our item at that position. Here are the steps:

1. Map each key to a big integer
2. The result is unique identifier numbers, but they are so large they exceed
   the number of slots in the hash table. We take the remainder (e.g. same principle as a roulette wheel)
3. The resulting hash values should be fairly __uniformly distributed__

##<a id="hashing">Hashing</a>

We do __hashing__ because of its speed.  If we know information about
the structure of our data (e.g. if a list is ordered or not), we can
use this knowledge to do efficient searches (e.g. in `O(log N)` runtime
using a _binary search_ instead of `O(N)`). We can get this even faster
with a technique called hashing.

If we know where every item should be, then our search can do a single
comparison to find the item.  This concept is called __hashing__ and it
has a really efficient runtime.  The idea is that when we input a key
(e.g. Will) through a __hash function__, this returns a map to where the
data is stored (aka the __hash values__, __hash codes__, __hash sums__,
__hashes__).

####<a id="hashtable">Hash Table</a>

A __hash table__ (aka __hash map__) is a collection of items that are
stored in a way where we can find the items very quickly.  Each position
in the hash table is called a __slot__ (sometimes the entire slot is
referred to as a __bucket__) and can hold an item.

What is a real life example of this? Say a teacher sorts their student's
papers in categories of A-Z based on their first names (or specific
buckets like W-Z if there are not a lot of names with those characters,
as long as we get about an even distribution). If there are multiple
people with the same first letter (e.g. Laura, Lisa), we have a hash
collision that can be fixed with a few different methods, with 
__chaining__ being the simplest (i.e. multiple names are added to the same category).

####<a id="hashfunction">Hash Function</a>

A __hash function__ is the function that distributes key/value pairs
across an array of slots.  A sample function would look like `index =
f(key, array_size)` and `hash = hashfunc(key)`.  The goal of the hash
function is to:

*  Be deterministic - i.e. equal keys produce the same hash value all the time 
*  Be efficient - Computing the hash should be quick 
*  Be uniform - distribute the keys uniformly so they do not cluster

####<a id="hashtimespace">Hash Time and Space</a>

A __perfect hash function__ is the case where every item is mapped to
a unique slot, which then runs in `O(1)` time.  We don't need a perfect
hash function in order for the hash to be efficient.

For worst case scenario (high number of collisions), the worst case
runtime is `O(N)` where `N` is the number of keys.

Another implementation of the hash table is with a balanced binary
search tree, which will give us `O(log N)` lookup time. This will
use less space since we no longer allocate a large array.

####<a id="hashexample">Hash Example</a>

Say we have a small __hash table__ represented by a list of 11 empty
slots (`m=11`) that have Python value of `None`.  Note that the number
of empty slots should ideally be __prime__ (so we can have an easier
way of resolving _hash collisions_, more on that later).

    slots       0    1    2    3    4    5    6    7    8    9    10
    values      None None None None None None None None None None None

Say we need to put in a set of integer items, like this: `54, 26, 93, 73`

We can create a __hash function__ that takes an item in the collection
and returns an integer that tells what slot (between `0` and `m-1`) to
place the item.  A common hash function is the __remainder method__,
where we take an item and divide by the table size, returning the
remainder as the hash value.

    54 % 11 = remainder of 10 26 % 11 = remainder of 4 93 % 11 = remainder
    of 5 73 % 11 = remainder of 7

After we calculate the hash values, we enter them into the hash table.
The __load factor__ is the `numberofitems / tablesize` (e.g. 4 items/
10 buckets).  Conceptually, if the _load factor_ is small, there are
potentially less collisions; if the _load factor_ is large, there are
potentially more collisions (which also means _collision resolution_
is more difficult). Usually a load factor of `.8` or higher is where
linear probing's performance really degrades and you should definitely do
`chaining`.  Here we have our updated hash table:

    slots       0    1    2    3    4    5    6    7    8    9    10
    values      None None None None 26   93   None 73   None None 54

####<a id="hashcollision">Hash Collision</a>

So what happens in our example if we add a new item of `71`?  We would get
a remainder of `5`, which would create a __hash collision__ with `93`;
this means at least two values are trying to fit into the same bucket
(in our example, the values of `93` and `71`).  We have two goals:

1. __minimize hash collisions__ - what can we do to prevent hash collisions?
2. __hash resolution__ - despite our best efforts to prevent hash collision, 
  we'll probably run into them; what can we do once a hash collision happens?

####<a id="hashminimizecollision">Minimizing Hash Collision</a>

As we look at different ways to minimize hash collision, we should keep
in mind that you want a function to __uniformly distribute__ hash values.
These can be tested using statistical tests (e.g. __Pearson's chi-squared
test__ for _discrete uniform distributions_)

####<a id="hashfolding">Minimizing Hash Collision with the Folding Method</a>

One way of trying to minimize hash collision is with the __folding
method__.

1. The idea is that we divide our items into equal sized pieces (with
the last piece probably not the same size).  e.g. if we have a phone
number of `436-555-4601`, we would take the numbers and divide them into
groups of 2, like `43, 65, 55, 46, 01`.  2. We then add all the pieces
together to get a resulting hash value.  e.g. `43+65+55+46+1 = 210`
3. With the resulting hash value (e.g. `210`), we do the modulus to get a
remainder of `1` (`210%11=1`) to place the value `210` into position `1`.
4. Optionally: Some _folding methods_ reverse every other piece before
the addition.  e.g. `43+65+55+46+01` turns into `43+56+55+64+01=219`,
which `219%11=10`

####<a id="hashmidsquare">Minimizing Hash Collision with the Mid-Square Method</a>

Another way of trying to minimize hash collision is with the __mid-square
method__.

1. The idea is that we first square the item, then extract some porition
of the resulting digits.  e.g. if the item is `44`, we calculate
`44^2=1936`.  2. The next step is extracting the middle two digits
(e.g. `93` from `1936`) and taking the modulus to get the remainder
(`93%11=5`)

####<a id="hashchars">Minimizing Hash Collision when Hashing Characters</a>

We can create hash functions for character-based items like strings.
For example, we have: `99+97+116=312`, then `312%11=4`.

    ord('c')  # 99 ord('a')  # 97 ord('t')  # 116

If we do a hash function that just simply adds up all the characters
and gets the modulus, we end up with _hash collision_ for anagrams
(e.g. `dad`).  We can fix this by adding a weighted position to the
character (e.g. multiply by 1 for first char, multiply by 2 for second
char, etc).  For example:

    def hash(astring, tablesize):
        sum = 0 for position in range(len(astring)):
            sum = sum * position + ord(astring[position])
        return sum%tablesize

###<a id="hashcollisionresolution">Collision Resolution</a>

__Collision resolution__ is the method for resolving _hash collision_,
which means what do we do when two or more items are hashed to the same
slot in the _hash table_.  There are many ways of attempting to address
this including __open addressing__ and __separate chaining__ (chaining).

####<a id="hashopenaddressing">Resolving Hash Collision with Open
Addressing</a>

__Open Addressing__ (aka __closed hashing__) is a method to resolve _hash
collisions_ by trying to find the next open slot in the _hash table_
through some __probe sequence__, which systematically visits each slot
until an open slot is found.  The name comes from the fact that the
location ('address') of the item is not determined by its 'hash value'.
The downside of _open addressing_ is that the number of stored entries
cannot exceed the number of slots.

There are many probe sequences, which has different takes on trying to
minimize clustering of hash values.  These probing sequences include:

*  __linear probing__ (aka __sequential probing__) - where the interval 
between probes is fixed (e.g. 1, every other)
*  __quadratic probing__ - where the interval
between probes is increased by adding the successive outputs of a
quadratic polynomial to the starting value given by the original hash *
* __double hashing__ - where the interval between probes is computed by
another hash function

Deletion in an open addressing scheme could get ugly since removing
one element might break a chain of insertions. We have no alternative
except to reinsert all the items in the run following the new hole.

                Hash Table      Hash Table
                (Expected)      (Worst Case)
    Search      O(n/m)          O(n)
    Insert      O(1)            O(1)
    Delete      O(1)            O(1)
    Successor   O(n + m)        O(n + m)
    Predecessor O(n + m)        O(n + m)
    Minimum     O(n + m)        O(n + m)
    Maximum     O(n + m)        O(n + m)

####<a id="hashlinearprobe">Resolving Hash Collision with Open Addressing
and Linear Probing</a>

When we visit each bucket one at a time, we are using a technique called
__linear probing__.  With the given example above, if we added `71`,
we would have a _hash collision_ since `93` is already in position `5`
(`71%11=5` and `93%11=5`).  We call this __rehashing__ when we try to make
`71` into a new hash.

    slots       0    1    2    3    4    5    6    7    8    9    10
    values      None None None None 26   93   None 73   None None 54

####<a id="hashquadraticprobe">Resolving Hash Collision with Open
Addressing and Quadratic Probing</a>

Instead of making the skip a constant value (e.g. 1, 2), we can use
a rehash function that increments the hash value by 1, 3, 5, 7, etc.
This means that if the first hash value is `h`, the successive hash
values are `h+1`, `h+4`, `h+9`, `h+16`, `h+25` etc.  This 'skip' value
is successive perfect squares.

####<a id="hashseparatechaining">Resolving Hash Collision with Separate
Chaining</a>

__Separate chaining__ (aka __chaining__) allows many items to exist at
the same location in the hash table.  When a hash collision happens,
the item does not do probing and instead is placed in the proper slot.
This is the easiest resolution, but devotes a considerable amount
of memory to pointers. This is space that could be used to make the
table larger and the 'lists' smaller.

When we search for an item, we use the hash function to generate the slot
where it should be.  Each slot has a collection so we use a searching
technique to decide if the item is present.  The advantage is that
on average, there are likely to be very few items in each slot so the
search should be more efficient. If there are many items in each slot,
we have a few ways to handle this:

* Separate Chaining with Linked Lists * Separate Chaining with List
Head Cells * Separate Chaining with other data structures, like a
self-balancing search tree (which adds complexity and may cause even
worse performance for smaller hash tables due to time spent inserting and
balancing the tree) * There is an 'array hash table' that uses a dynamic
array that resizes to the size it needs (or say doubling the array size).

Here is some sample code for a hash table implementation:

HashTable Implementation

    class SlotItem(object):
        """ An items in the same hash table slot """
        def __init__(self, key, value):
            self.key = key
            self.value = value

        def __str__(self):
            return self.key

    class HashTable(object):
        """
            Implementation of a hash table using chaining instead of
            open addressing.  Each slot has a list of items that are
            appended when there is a hash collision.
        """

        def __init__(self, size):
            self.size = size
            self.table = [[]] * self.size

        def hash_function(self, key):
            """
                Return modulus (i.e. remainder) and handles two scenarios:
                1 - numbers only (int or long); just take modulus 2 -
                characters involved; create hash from position and ordinal
                    value of character (position added to handle anagrams)
            """

            if type(key) is int or type(key) is long:  # numbers only
                return key % self.size
            else:
                total = 0  # characters involved for position in xrange(len(key)):
                total = total + (ord(key[position]) * (position+1))
                return total % self.size

        def set(self, key, value):
            """
                Finds slot with hash_function, then saves key-value pair.
                If there is an existing key, we overwrite that value.
            """ index = self.hash_function(key)  # find correct slot

            for slot in self.table[index]:  # look inside slot for key
                if slot.key == key:  # key found, replace current value
                    slot.value = value return
            self.table[index].append(SlotItem(key, value))  # key not found, add

            self.check_load_factor(upper_limit=.8, resize=2)

        def get(self, key):
            """ Finds slot with hash_function, returns slot value or
            else None """
            index = self.hash_function(key)

            for slot in self.table[index]:  # look inside slot for key
                if slot.key == key:  # key found, return current value
                    return slot.value
            return None  # no key found

        def remove(self, key):
            """ Given a key, remove the key-value pair """
            index = self.hash_function(key)
            for i, slot in enumerate(self.table[index]):
                if slot.key == key:  # key found, return current value
                    del self.table[index][i]

        def check_load_factor(self, upper_limit=.8, resize=2):
            """
                Check load factor, if limit reached, warn to resize
                larger table
            """ load = 0 for i in xrange(self.size):
                for slot in self.table[i]:
                    if slot:
                        load += 1

            load_factor = float(load)/float(self.size)
            #print "Load factor is ", load_factor

            if load_factor > upper_limit:  # need to resize for larger hash table
                print "Load Factor is past upper limit, you should resize"
                # TODO: Create deepcopy, dynamically resize for high and low limit

            else:
                pass  # load_factor is in acceptable limits


Tests for Hashtable Implementation

    """ Implement a hashmap (with amortized constant time look-ups)
    without using a hashmap primitive.  Includes an executable testing
    framework for the data structure """


    import unittest from nose.tools import assert_equal, assert_not_equal,
    assert_true, assert_false


    class TestUnitHashTable(unittest.TestCase):
        """ Unit Tests - Test each piece in isolation """

        def setUp(self):
            self.ht = HashTable(11) self.a = SlotItem('hello', 1112223333)
            # char, int scenario self.b = SlotItem(1, 1)  # int, int
            scenario self.c = SlotItem('f', 'morestuff')  # char, char
            scenario self.ht.table = [[self.a], [self.b], [], [self.c],
                                      [], [], [], [], [], [], []]

        def test_keys_can_be_integers(self):
            try:
                self.ht.set(11, 100)
            except TypeError:
                self.fail("UT: Hashing func cannot handle key with ints")

        def test_keys_can_be_characters(self):
            try:
                # hello = 104*1 + 101*2 + 108*3 + 108*4 + 111*5 = 1617 =>
                %11=0 self.ht.set('hello', 'world')  # pos: 'hello'(0), value: 'world'
            except TypeError:
                self.fail("UT: Hashing func cannot handle key with chars")

        def test_keys_can_be_spaces(self):
            try:
                self.ht.set('  ', 'somevalue')
            except TypeError:
                self.fail("UT: Hashing func cannot handle key with
                spaces")

        def test_keys_can_be_mix_chararacters_and_integers(self):
            try:
                self.ht.set('a1b3e', 'somevalue')
            except TypeError:
                self.fail("UT: Hashing func cannot handle key with ints & chars")

        def test_hashes_anagram_keys_to_different_buckets(self):
            """ Ensure hashing of keys with anagrams turns out unique
            """ self.ht.set('elvis', 'samestuff')  # 1666 => %11=5
            self.ht.set('lives', 'samestuff')  # 1651 => %11=1

            bucket5 = self.ht.table[5]  # elvis bucket goes to slot 5
            bucket1 = self.ht.table[1]  # lives bucket goes to slot 1

            for _ in bucket5:
                if _.key == 'elvis':
                    value5 = _.value

            for _ in bucket1:
                if _.key == 'lives':
                    value1 = _.value

            assert_equal(value5, value1)

        def test_identify_empty_hash_table_index(self):
            # Check if list in this bucket is empty
            bucket2 = self.ht.table[2] assert_false(bucket2)

        def test_identify_filled_hash_table_index(self):
            # Check if list in this bucket is empty
            bucket1 = self.ht.table[1] assert_true(bucket1)

        def test_get_existing_hash_table_item(self):
            value = self.ht.get('hello') assert_equal(value, 1112223333)

        def test_get_nonexisting_hash_table_item(self):
            value = self.ht.get('nothere') assert_equal(value, None)

        def test_set_data_on_empty_hash_table_slot(self):
            assert_equal(self.ht.get(16), None) self.ht.set(16, 'abc')
            assert_equal(self.ht.get(16), 'abc')

        def test_set_data_on_existing_hash_table_slot(self):
            assert_equal(self.ht.get('f'), 'morestuff')
            self.ht.set('f', 'differentstuff')
            assert_equal(self.ht.get('f'), 'differentstuff')

        def test_remove_data_on_existing_key_with_value(self):
            assert_equal(self.ht.get('hello'), 1112223333)
            self.ht.remove('hello') assert_equal(self.ht.get('hello'), None)

        def test_remove_data_on_nonexisting_key(self):
            try:
                self.ht.remove('notthere')
            except:
                print "Issue with trying to remove a nonexisting key"
                raise

        def test_set_load_factor_high(self):
            assert_equal(self.ht.size, 11)
            self.ht.table = [[self.a], [self.b], [self.a], [self.c], [self.a],
                             [self.c], [], [self.a], [self.a], [self.b], []]
            self.ht.set(10, 'here')
            # TODO: if more time, trigger auto resize
            #assert_equal(self.ht.size, 22)
            #assert_equal(self.ht.get(10), 'here')  # check value here after resize
            # TODO: check hash value not too high
            # TODO: check uniform distribution of values

    class TestFunHashTable(object):
        """ Functional Tests - Test all pieces together from end to end
        """

        def test_end_to_end(self):
            hash_table = HashTable(11)
            print "FT: Created Hash Table of size, ", hash_table.size

            print "FT: Default value type at slot 0 is None"
            assert_equal(hash_table.get(0), None)

            print "FT: Setting value in slot 13 (i.e. 13%11=2)
            with value "dee":
                hash_table.set(13, 'dee')
            assert_equal(hash_table.get(13), 'dee')

            print "FT: Adding value in slot 3 with value dee"
            hash_table.set(3, 'dee') assert_equal(hash_table.get(3), 'dee')

            print "FT: Checking that two values are the same"
            assert_equal(hash_table.get(13), hash_table.get(3))

            print "FT: Deleting value in slot 2" hash_table.remove(2)
            assert_equal(hash_table.get(2), None)


    if __name__ == '__main__':

        # run unit tests suite =
        unittest.TestLoader().loadTestsFromTestCase(TestUnitHashTable)
        unittest.TextTestRunner(verbosity=2).run(suite)

        A = TestFunHashTable()
        A.test_end_to_end()


###<a id="stringmatching">String Matching via Hashing</a>

__Strings__ are sequences of characters where the order of the character matters.
The primary data structure for representing strings is an array of characters.
We get a constant-time access to the `ith` character of the string.

You'll often run into a substring search. There's different approaches, including:

* quadratic bound worst-case scenario of searching the presence of pattern `p` in
text `t` at every position in the text
* a better performing (linear _expected-time_) called the __Rabin-Karp__ algorithm,
  based on hashing (i.e. compute a hash function on both the pattern string `p` and
  the `m` character substring starting from the `i`th position of `t`. If these
  two strings are different, the hash values will almost certainly be different.
  False positives are so rare that we can easily spend `O(m)` time to check.

###<a id="duplicatedetectionviahashing"> Duplicate Detection Via Hashing</a>

The key idea of hashing is to represent a large object (e.g. a key, a string, a substring)
using a single number. This representation of a large object is to be able to manipulate
the object in constant time.

Hashing is used most often in speeding up search, including:

* Is a given document different from all the rest in a large corpus?
* Is a part of this document plagiarized from a document in a large corpus?
* How can I convince you that a file hasn't changed?

Hashing is a fundamental idea in randomized algorithms, yielding linear expected-time algorithms.

##<a id="specializeddatastructures>Specialized Data Structures</a>

We have basic data structures that represent an unstructured set of items so
we can run retrieval operations.

There's also another set of data structures that represent more structured or
specialized kinds of objects, like points in space, strings, and graphs. These include:

* __string data structures__ - character strings are usually represented by
  arrays of characters. __Suffix trees/arrays__ are special data structures that
  preprocess strings to make pattern matching operations faster
* __geometric data structures__ - are usually collections of data points and regions.
  regions in the plane can be described by polygons, usually made up of a chain of line segments.
  Spatial data structures like __kd-trees__ organize points and regions by geometric
  location to support fast search. 
* __graph data structures__ - are usually represented using either __adjacency matrices__
  or __adjacency lists__. The choice of representation can have a substantial impact on
  the design of the resulting graph algorithms.
* __set data structures__ - are subsets of items that are typically represented using
  a dictionary to support fast membership queries.

##<a id="linkedlists">Linked Lists</a>

So remember that a __list__ is a mutable data structure that can hold
different data types in the same list (e.g. int, then a str) And remember
that an __array__ is a mutable data structure that can only hold the
same data types in the same array (e.g. only strings) So with an array,
memory allocation is done for all of its elements as one block of memory
(easier since its all the same data types).  A linked list instead does
memory allocation so that each element (aka __linked list element__,
__node__), gets its own memory block.  The linked list gets its overall
structure by using __pointers__ to connect all its nodes together.

So what are some examples of linked lists?

* The list is the simplest kind of linked structure. The only operations
  for this are searching, inserting, and deleting.
* Singly Linked List - a scavenger hunt where one clue points to the
  place of the next clue (but that clue has no reference to the previous
  clue)
* Doubly Linked List - a train car that has a previous and next
  train car connected to it. Each node points to both the predecessor
  and its sucessor element
* Circular Linked List - Whose turn is it on a multiplayer game 
  (keeps going around) or resource pooling on a shared 
  resource (e.g. round robin scheduling)

In terms of implementation, we can search for an item in a linked list
either iteratively or recursively.

###<a id="pointers">Pointers</a>

Before we get too in-depth with linked list/structures, we need to get
into detail about pointers. __Pointers__ are the connections that hold
the pieces of linked structures together.

Pointers represent the address of a location in memory. A variable
that stores a pointer to a given data item can provide more freedom
than storing a copy of the item itself.

A real life example might be a cell-phone number can be thought of as a
pointer to its owner as they move around.

In programming languages like C, we have:

* pointer `p` is assumed to give the address in memory where a chunk of data is located
* pointers in C have types declared at compile time, denoting the data type it can point to
* we use `*p` to denote the item that is pointed to by pointer `p`
* we use `&x` to denote the address of (i.e. pointer to) of a particular variable `x`.
* a `NULL` pointer value is used to denote structure-terminating or unassigned pointers

###<a id="createlinkedlist">Creating a Linked List</a>

Each node contains two fields:

* a 'data' field to store the actual value.  * a 'next' field, which is
a pointer used to link the node to the next node

##<a id="stacksqueues">Stacks and Queues</a>

A __container__ is a data structure that lets you store and retrieve
data items _independent of content_. Containers are in contrast to dictionaries,
which are abstract data types that retrieve based on key values or content.
There are a few types of containers and they're identified by
the retrieval order. The two most important are:

* __Stacks__ - Stacks are containers that retrieve content based on 
Last-In, First-Out (LIFO) order. Stacks are simple to implement and
very efficient. Choose to use a stack when the retrieval order doesn't
matter at all. The `put` and `get` operation for stacks are called
`push` and `pop`. An example is a subway car where the people that come
in first have the hardest time getting out during busy hours.
* __Queues__ - Queues are containers that retrieve content based on
First-In, First-Out (FIFO) order. This order is useful when you want to
minimize the _maximum_ time spent waiting (Note: the _average_ time waiting
will still be the same for FIFO or LIFO). Queues are trickier to implement
than stacks, but they're the right container to choose when the order is
important. The `get` and `put` operations in queuse are usually called
`enqueue` (insert item x at the back of the queue q) or `dequeue` (return
and remove the front item from queue q). Queues are the fundamental
data structure controlling __breadth-first searches__ in graphs.

You can implement a stack or queue using either arrays or linked lists.

###<a id="stacksqueuespython">Stacks and Queues in Python</a>

In Python, you can use a list as a __stack__ very easily (just run
`mylist.append(myitem)` or `mylist.pop(myitem)`.

In Python, if you need a __queue__, just use the `collections.deque`.
You can use a list as a queue, but it's not efficient (due to inserts
or pops from the beginning of a list since all the other elements have 
to be shifted by one). With a __deque__, you can run `get` and `put` with:
`myqueue.popleft()` and `myqueue.append(myitem)`

##<a id="dictionaries">Dictionaries</a>

The __dictionary__ data type allows access to data items by content.
The idea is that you stick an item into a dictionary so you can find it later.
The primary operations on a dictionary are:

* __Search__ - in a dictionary `D` and search key `k`, return a pointer to the element
  in the dictionary whose key value is `k` (if one exists)
* __Insert__ - in a dictionary `D` and a data item `x`, add it to the set in the dictionary
* __Delete__ - in a dictionary `D` and given a pointer to a given data item `x`, remove the
  data item from the dictionary 
* __Max__ or __Min__ - Retrieve the item with the largest (or smallest) key from the dictionary
  This is important because the dictionary can serve as a __priority queue__.
* __Predecessor__ or __Successor__ - Retrieve the item from the dictionary whose key is
  immediately before (or after) the item in sorted order. These methods allow us to
  iterate through the elements of the data structure

###<a id="dictionaryexamples">Dictionary Examples</a>

A lot of common data processing tasks take dictionaries. For example, if we want
to remove all duplicate names from a mailing list, we can:

1. Initialize an empty dictionary where the search key is the record name
2. Iterate through the mailing list
3. For each record, _search_ to see if a name exists in the dictionary
4. If there is no record, then _insert_ the record
5. Once we finish iterating through the mailing list, we need to extract the names out of the dictionary
6. We start from the first item with _Min_, then repeatedly call _Successor_ until we obtain _Max_.

When we define a problem in terms of an abstract dictionary's operations, we can avoid
the details of the data structure's representation and instead focus on the task

###<a id="dictionaryuses">Dictionary Uses</a>

There are various dictionaries based on:

* Arrays
* Linked Lists
* Binary Search Trees
* Hash Tables

###<a id="dictionaryruntimes">Dictionary Runtimes</a>

Remember that a data representation can have some efficient operations while
other operations are more expensive. A lot of these operation costs depend
on whether the data structure is sorted or unsorted.

####<a id="dictionaryruntimesarrays">Dictionary Runtimes (Sorted Array and Unsorted Arrays)</a>

Dictionary runtimes as a __sorted array__ and __unsorted array__ are:

                    Unsorted array  Sorted array
    Search          O(n)            O(log n)
    Insert          O(1)            O(n)
    Delete          O(1)*           O(n)
    Successor       O(n)            O(1)
    Predecessor     O(n)            O(1)
    Minimum         O(n)            O(1)
    Maximum         O(n)            O(1)

You can see that the implementation of a dictionary using a sorted array
completely reverses our notations of what is easy and what is hard.

In a sorted array, insertion and deletion become more expensive because 
making room for a new item or filling a hole requires moving around a lot of items.
While in a sorted array, get methods like search and successor and minimum/maximum
are quick operations.

When designing data structures, make sure to balance all the different
operations it supports. The fastest data structure to support both
operations A and B might not be the fastest data structure to support
either operation A or B.

####<a id="dictionaryruntimeslists">Dictionary Runtimes (Lists)</a>

A dictionary can be implemented as __sorted list__ or __unsorted list__.
A dictionary can also be implemented as a __singly-linked list__ or __doubly-linked list__.

We then have:

* a __singly-linked unsorted list__
* a __double-linked unsorted list__
* a __singly-linked sorted list__
* a __doubly-linked sorted list__

Dictionary runtimes as a the above are:

                    Singly      Double      Singly      Doubly
                    Unsorted    Unsorted    Sorted      Sorted
    Search          O(n)        O(n)        O(n)        O(n)
    Insert          O(1)        O(1)        O(n)        O(n)
    Delete          O(n)*       O(1)        O(n)*       O(1)
    Successor       O(n)        O(n)        O(1)        O(1)
    Predecessor     O(n)        O(n)        O(n)*       O(1)
    Minimum         O(n)        O(n)        O(1)        O(1)
    Maximum         O(n)        O(n)        O(1)*       O(1)

Same as unsorted arrays, search operations are slow when we have unsorted
while maintenance operations are fast.

###<a id="dictionaryruntimesbinarysearchtrees">Dictionary Runtimes (Binary Search Trees)</a>

So far we've seen data structures that allow either fast search OR flexible updates, but
not both fast search AND flexible updates.

__Binary Search__ requires that we have fast access to _two elements_, being the
median elements above and below the given node. We create this by using a 'linked list'
with two pointers per node and that gives us __binary search trees__.

There's a __rooted binary tree__ that is recursively defined as either:

1. empty
2. consisting of a node called the root, together with two rooted binary trees
   called the left and right subtree (The order matters among 'brother' nodes in rooted trees)

A binary search tree labels each node in a binary tree with a single key (say `x`).
The labeling scheme is special, with:

* Any nodes in the left subtree of `x` have keys `< x`.
* Any nodes in the right subtree of `x` have keys `> x`.
* For any binary tree on `n` nodes, and any set of `n` keys, there is exactly one labeling
  that makes it a binary search tree.

####<a id="binarysearchtreeexamples">Binary Search Tree Examples</a>

For example, on a three node tree, there are five distinct binary search trees:

      3     3       2      1       1
     2     1       1 3      3       2
    1       2              2         3

####<a id="binarysearchtreeimplementation">Binary Search Tree Implementation and Operations</a>

Binary tree nodes have a:

* __left__ pointer field
* __right__ pointer field
* an optional __parent__ pointer field
* a __data__ field

Binary trees have the following basic operations:

* __Searching__ - we start at the root and either return the root node `x`, or we proceed `left` (< x) or `right` (> x)
                  Remember that both the left and right subtrees of a binary search tree are also binary search trees
                  We get `O(h)` runtime where `h` is the height
* __Minimum and Maximum__ - it's easy to find the `minimum` and `maximum` element in the tree.
                  The smallest key is in the leftmost subtree of the root and largest key is in the rightmost subtree of the root
* __Traversal__ - traversal means to visit all the nodes and runs on `O(n)` time where n is the number of nodes in the tree
* __Insertion__ - there's only one place to insert an item `x` into a binary search tree `T` where it'll know how to find it again
                  Insertion runs on `O(h)` time
* __Deletion__ - Deletion is tricky because removing a node means appropriately linking its two descendant subtrees
                 back into the tree somewhere else. There are three cases: 1) no children, easy to handle, just delete
                 2) deleted node only has one child; link the child node to the parent node
                 3) deleted node has two children; relabel this node with the key of its immediate successor in sorted order.
                    The successor must be the smallest value in the right subtree (leftmost descendant in the right subtree)

####<a id="binarysearchtreeruntimes">Binary Search Tree Runtimes</a>

All three dictionary operations take `O(h)` time, where `h` is the height of the tree.
The smallest height we can hope for is when the tree is perfectly balanced, where
`h = log n`. The issue is that the tree has to be perfectly balanced.

With binary search trees, the order that we insert data greatly affects runtimes (since
that's the shape/height we create for our tree)

For example, if we insert our data that has been sorted, we have `n` list items with an
`n` tree. Say we have a list of `[5, 9, 11, 12]`. This creates a binary tree of:

    5
     9
      11
       12

So with the above example, we have `n` runtime because we only have right insertions,
creating a skinny tree. Our implementation depends a lot on __randomization__ and hoping
for the best results based on the input.

There are different implementations of binary search trees caused __balanced binary search
trees__, such as __red-black trees__ and __splay trees__ that guarantee the height always
be `O(log n)` (by adjusting the tree a little after each insertion / keeping it close enough
to be balanced).

##<a id="priorityqueues">Priority Queues</a>

Many algorithms process items in a specific order. __Priority Queues__ are data structures
that provide more flexibility than simple sorting; instead they allow new elements to
enter a system at arbitrary intervals. It's cost-effective to insert a new job into a priority
queue than to re-sort everything on each new item's arrival. So the idea is that you can
add items at any time, but only the item with the highest priority can be removed first.

Priority Queues are often implemented with heaps since we're sure that the value of a node is less
than all the values in its subtrees. The smallest element is the one with the highest priority.

We have three primary operations:

* __Insert__ - given an item `x` with key `k`, insert it into the priority queue `Q`
* __Find Min or Max__ - returns a pointer to the item whose key value is smaller or larger
than any other key in the priority queue `Q`
* __Delete Min or Max__ - remove the item from the priority queue Q whose key is the
minimum or maximum

###<a id="priorityqueueexample">Priority Queue Example</a>

Examples of priority queues could be:

* patients in an emergency room; can keep adding, but only the one with the highest emergency gets sent to ER

###<a id="priorityqueueruntimes">Priority Queue Runtimes</a>

When the data structure is: an unsorted array, a sorted array, or a balanced search tree,
the runtimes are:

            Unsorted        Sorted      Balanced
            Array           Array       Tree
Insert      O(1)            O(n)        O(log n)
Find Min    O(1)            O(1)        O(1)
Delete Min  O(n)            O(1)        O(log n)

Notice how find minimum is in constant time for each of these data structures.
The trick is to use an extra variable to store the pointer/index to the minimum
entry in each of these structures so we can return this value whenever we're asked
to find the minimum.

##<a id="algorithmdesign">Designing Algorithms</a>

We briefly cover the structure of data, then go into a couple of design
approaches with __incremental__ and __divide and conquer__, which are
opposites of each other.

*  __incremental approach__ (aka __iteration__) is used in algorithms
like _insertion sort_.  This means working with _iterables_, objects
that can be used in `for` or `while` loops.  *  __divide and conquer
approach__ breaks the problem into several subproblems that are similar
to the original problem, but smaller in size.  This is used in algorithms
like _merge sort_.

####<a id="incremental">Approach: Incremental</a>

__Incremental__ is the repetition of a block of statements.  An example
is:

    a = 0 for i in range(10):  #0, 1, 2...8, 9
        a+=1
    print a  #10

####<a id="loopinvariant">loop invariant</a>

As we create our loops, we need to be aware of __loop invariants__
(aka __invariant__), which simply means that these general conditions
have to be true.

1. __initialization__ means it is true before the first iteration of the
loop 2. __maintenance__ means it remains true before the next iteration
3. __termination__ means when the loop terminates, the invariant gives
us a useful property that helps show that the algorithm is correct.

####<a id="divideandconquer">Approach: Divide and Conquer</a>

The __divide and conquer__ approach is to break apart a large problem
into several subproblems that are similar to the original problem but
smaller in size.  We solve the subproblems __recursively__ (i.e. they
call themselves) until they reach the __base case__, and then combine
these solutions to create a solution to the original problem.

1. __divide__ means to split the problem into subproblems that are
smaller instances of the same problem.  2. __conquer__ means solving
the subproblems recursively.  If the subproblem size is small enough,
just solve the subproblems in a straightforward manner.  3. __combine__
means to combine the subproblem solutions into the solution for the
original problem.

####<a id="recursion">recursion</a>

__Recursion__ is a method where the solution to a problem depends on
solutions to smaller instances of the same problem.  You can visually see
this as a __recursion tree__, which is a tree diagram of recursive calls.
Recursion has to obey three laws:

1. A recursive algorithm must have a __base case__.  2. A recursive
algorithm must changes its state and move toward the base case.  3. A
recursive algorithm must call itself, recursively.

__Recursive Example of calculating the Fibonacci number__

    def fib(n):
        """ return the Fibonacci number """
        if n==0:
            return 0
        elif n==1:
            return 1
        else:
            return fib(n-1) + fib(n-2)

__Recursive Example of Removing Duplicates Letters next to each other__

    def removeDups(word):
        if len(word) <= 1:
            return word
        elif word[0] == word[1]:
            return removeDups(word[1:])
        else:
            return word[0] + removeDups(word[1:])
        word = 'aaaabbbbbcccdd'
        print word print removeDups(word)  # abcd

Other examples of recursive problems include:

*  The Tower of Hanoi *  Exploring a Maze

##<a id="sortingandsearching">Sorting and Searching</a>

__Sorting__ is the basic building block that many other algorithms are
built around. By really understanding sorting, we can then understand
how to solve other problems. A lot of the interesting ideas in the design
of algorithms appear in the context of sorting (e.g. divide-and-conquer,
data structures, randomized algorithms).

There are several fundamental sorting algorithms, including:

* __heapsort__
* __mergesort__
* __quicksort__
* __distribution sort__

###<a id="comparisonsortalgorithms">Sorting Algorithms - Comparison Sorts</a>

A basic computational problem is the __sorting problem__, where you sort
a sequence of _n_ numbers (aka __keys__).  We apply the above general
approaches (_insertion_, _divide and conquer_) using different types of
algorithms.  The following algorithms (_insertion sort_, _bubble sort_,
_selection sort_, _merge sort_, _heapsort_, and _quicksort_) are all
__comparison sorts__ (i.e. they determine the order of an input array
by comparing elements).

How to use Sorting:

A really important algorithm design technique is to use sorting as
a basic building block because many other problems become easy once a set
of items is sorted.

###<a id="sortingruntimes">Sorting Runtimes</a>

Clever sorting algorithms exist that run in `O(n log n)` whereas naive
sorting algorithms are really slow with `O(n^2)`. Let's look at some sample
numbers for n:
    
    n           n^2/4           n lg n
    10          25              33
    100         2500            664
    1000        250000          9965
    10000       25000000        132877
    100000      2500000000      1660960

###<a id="applicationsofsearching">Applications of Searching</a>

Some sample applications of searching are:

* __Searching__ - Search processing, meaning that we do a binary search test on whether 
  an item is in a dictionary in `O (log n)` time, providing keys are all sorted
* __Closest Pair__ - Given a set of `n` numbers, how do you find the pair of numbers
  that have the smallest difference between them. Once the numbers are sorted, the
  closest pair of numbers must lie next to each other somewhere in sorted order.
  A linear-time scan through the sorted numbers completes the job for a total of `O (n log n)`
  time, including the sorting
* __Element Uniqueness__ - Are there any duplicates in a given set of `n` items?
  This is a special case of the above closest-pair problem, where the pair separated
  is by a gap of zero. The most efficient algorithm sorts the numbers and then does a linear
  scan through checking all adjacent pairs
* __Frequency Distribution__ - Given a set of `n` items, which elements occurs the largest
  number of times in the set. If the items are sorted, we can sweep from left to right
  and count them, since all identical items will be lumped together during sorting
* __Selection__ - What is the `k`th largest item in an array? If the keys are placed in
  sorted order, the `k`th largest can be found in constant time by simply looking at the
  `k`th position of the array.
* __Convex Hulls__ - What is the polygon of smallest area that contains a given set of `n`
  points in two dimensions? Think of the convex hull as a rubber band stretched over the
  points in the plane and then released. Basically, the convex hull gives a nice presentation
  of the shape of the points and is needed to build more complicated geometric algorithms.
  Once you have the points sorted by the `x` coordinate, the points can be inserted from
  left to right into the hull. Since we know that the right-most point is always on the boundary,
  we know the point will appear in the hull. Adding a new point might cause another point to
  be deleted, but even then the total time is linear after the sorting has been done.

####<a id="sortingexample">Sorting Example Problem</a>

Say you have two sets (size `m` and size `n`) and need to determine if they're disjoint (i.e.
no element in common). Analyze the worst-case complexity in terms of `m` and `n` if `m` is
substantially smaller than `n`. You have a few different algoirthms that you can do, including:

* First sort the big set - sort the big set in `O(n log n)`, then do a binary search with each
  of the `m` elements in the second, looking to see if it exists in the big set.
  Total time is `O((n+m) log n)`
* First sort the small set - sort the small set in `O(m log m)`, then do a binary search with each
  of the `n` elements in the big set, looking to see if it exists in the small one.
  Total time is `O((n+m) log m)`
* Sort both sets - If we sort both sets, we don't have to do binary search to detect a common element.
  We can compare the smallest elements of the two sorted sets, and discard the smaller one if they are
  not identical. Cost is `O(n log n + m log m + n + m)`.

####<a id="pragmaticsofsorting">Pragmatics of Sorting</a>

So now that we know the importance of sorting and how to start sorting, we need to ask ourselves
what order do we want our items sorted? Some questions to ask are:

* Increasing or Decreasing Order; different applications call for different orders
* Sort just the key or the entire record? - Sorting a data set involves maintaining the
  integrity of complex data records. Sometimes you might want to just sort the key, as in
  say the name given a mailing list of records with names, addresses, and phone numbers.
* Equal keys - what do we do about equal keys? You can bunch together equal key values
  or you can create a relative order among these keys. Sometimes you can resort to __secondary keys__,
  like by Last Name, then First Name. A sorting algorithm is __stable__ if you want to leave items 
  in the same relative order as the original permutation. Usually faster algorithms are not stable.
* The right way to specify what matters in your sorting algorithm is with an application-specific
  pairwise-element comparison function (i.e. is a < b, a > b, a = b). We just pass items to this
  sorting function and it'll tell you how to sort. Usuaully your language has a sort method that
  you should use instead of building your own.

####<a id="whylearnsorting">Why learn Sorting?</a>

Again, you might wonder why we want to learn the different ways of sorting when you're usually
better off not implementing them and instead should use a built-in library function instead.
The answer is that these __design techniques__ are very important for other algorithm problems
that you will encounter.

####<a id="insertionsort">Incremental: insertion sort</a>

__Insertion sort__ is a simple sorting algorithm based on the incremental
approach and is efficient at sorting a small number of elements in
place and is useful when items are mostly sorted already.  The idea is
to always maintain a sorted sublist in the lower portions of the list;
what this means is we will have a 'sorted' and an 'unsorted' group. See
example steps:

     sorted|unsorted
          0|5,3,-1
        0,5|3,-1
      0,3,5|-1
    -1,0,3,5|

So what are we doing?  We move the items from our 'unsorted group'
to the 'sorted group' (this is where the new item is then 'inserted')
into the sorted group.
  Notice we have a base case where the first item is sorted (because it
  is the only item in the sorted group)
When we move from unsorted to sorted one item at a time, it simply does
a comparison of the next unsorted item with the next sorted item(if it
is larger, then insert item after your comparison item).  Basically,
we move this invisible line that divides the "sorted" vs the "unsorted"
groups one item at a time.

For example, if we want to sort a hand of playing cards:

1. Start with an empty left hand and all cards face down on the table
2. We remove one card from the table and insert it into the correct
position on the left hand (first card is the base case, its already
sorted then) 3. To find the correct position, we compare it with each
of the cards already in the left hand (from right to left); this way
left hand cards are always sorted

Example Code:

    """ Insertion Sort """

    def insertionSort(mylist):
        for index in range(1, len(mylist)):
            print "Index is ", index  # 1, 2, 3, 4, 5, 6, 7, 8; this is the outer loop

            # setup first case (only one item) currentvalue =
            mylist[index] position = index

            # this is the inner loop, loops through the sorted list backwards and compares values
            while position > 0 and mylist[position-1] > currentvalue:
                mylist[position] = mylist[position-1] position = position - 1

            # found spot in inner sorted loop to place item
            mylist[position] = currentvalue


    if __name__ == '__main__':
        mylist = [54,26,93,17,77,31,44,55,20]
        print "Original: ", mylist
        insertionSort(mylist)
        print "Insertion Sorted: ", mylist

So why would you want one of the O(n^2) algorithms when there are O(n
log n) algorithms? Well, insertion sort is good when:

* The data is nearly sorted (because it is adaptive) so the sort becomes
O(n) * When the list is small (low overhead compared to others) * Other
algorithms like quicksort make a big assumption that the data is already
in memory. If the data source you're reading from is really slow, you
might want to "sort as you go" instead.


####<a id="bubblesort">Incremental: bubble sort</a>

__Bubble sort__  (aka __sinking sort__, __ripple sort__) is a simple but
inefficient sorting algorithm that repeatedly goes through the list to
be sorted, compares each pair of adjacent items, and swaps them if they
are in the wrong order.

Here is a sample run:

    # first pass 5 1 4 2 8
    # original 1 5 4 2 8
    # swap the first pair
    (5, 1) 1 4 5 2 8  # check to swap the next pair (4, 5), but no swap needed
    (4 < 5) 1 4 2 5 8  # swap 5 and 2 1 4 2 5 8
    # check to swap the next pair (5, 8), but no swap needed (5 < 8)

    # second pass 1 4 2 5 8 1 2 4 5 8
    # swap 2 and 4 1 2 4 5 8
    # check to swap the next pair (4, 5), but no swap needed
    (4 < 5) 1 2 4 5 8  # check to swap the next pair (5, 8), but no swap needed (5 < 8)

    # third pass checks through each pair, but no swaps needed since its sorted

For example, say we were sorting scrabble tiles into alphabetical order.

1. Place letters on tile holder and look at the first block.  2. Look
at the block to the right of it.  3. If the block to the right should
come before the block on the left, swap them.  4. Compare the next block
in line with the first and repeat step 3 5. Begin step 1 again with the
second block

The name bubble sort is because elements tend to move up into the correct
order like bubbles rising to the surface and you see a rippling effect
for the ones that are not in the correct order.  After each pass of the
bubble sort, one item is definitely sorted; a total of `n-1` passes to
sort `n` items.  Big O Runtime is `O(n^2)`.

Example Code

    # Bubble Sort def bubbleSort(mylist):
        for passnum in range(len(mylist)-1, 0, -1):
            #print passnum
            # backwords (8,7,6,..2,1) b/c other items are already sorted
            for i in range(passnum):
                if mylist > mylist[i+1]:  # compare neighbors
                    mylist, mylist[i+1] = mylist[i+1], mylist  # swap

    if __name__ == '__main__':
        mylist = [54,26,93,17,77,31,44,55,20]
        print "Original: ", mylist
        bubbleSort(mylist) print "Bubble Sorted: ", mylist

####<a id="selectionsort">Incremental: selection sort</a>

__Selection sort__  improves on _bubble sort_ by making only one exchange
for every pass through the list.  The selection sort finds the largest
value as it makes its pass and after completing the pass, places it
in the correct location/order.  What happens is that there is a 'swap'
(where we put the largest value into the largest index; the item that
was previously in the largest index is swapped over to where the previous
largest value was).

Here is an example run:

    #sorted | unsorted 64 25 12 22 11
    # initial state 11|25 12 22 64
    # find smallest value (11) and swap with first element (64) 11 12|25 22 64
    # find next smallest value from unsorted (12) and compare with second element (25),
      do swap (12 < 25) 11 12 22|25 64
    # find next smallest value from unsorted (22) and compare with third element
      (25), do swap (22 < 25) 11 12 22 25|64  # find next smallest value
    #from unsorted (25) and compare with fourth element (25), no need to swap (25 < 64) 11 12 22 25 64|
    # all sorted

Similar to bubble sort, after the initial pass, the largest item appears
in place.  The final item is in place after `n-1` passes to sort `n`
items.  This is slightly faster than bubble sort since we don't have to
do as many exchanges.  Big O Runtime is still `O(n^2)`.


    """ Selection Sort from largest to smallest """

    def selectionSort(mylist):
        for fillslot in range(len(mylist)-1, 0, -1):
            #print fillslot
            # backwords (8,7,6,..2,1) b/c other items are already sorted
            positionOfMax = 0 for i in range(1, fillslot+1):
                if mylist[i] > mylist[positionOfMax]:  # is value greater than value at max
                    positionOfMax = i

            # to move the largest value to the largest index,
            # we 'swap' the item # currently in the largest index
            position mylist[fillslot], mylist[positionOfMax] = mylist[positionOfMax], mylist[fillslot]


    if __name__ == '__main__':
        mylist = [54,26,93,17,77,31,44,55,20]
        print "Original: ", mylist
        selectionSort(mylist)
        print "Selection Sorted: ", mylist


The algorithm can be changed to swap out for the smallest item instead of
the largest item (depending on how you want to sort by). If so, the idea
is the same; we select the smallest unsorted item (instead of largest)
and then swap it with the item in the next position to be filled.

We look through the entire array for the smallest element, once you
find it you swap it (smallest element with the first element of the
array). Then you look for the smallest element in the remaining array (the
array without the first element) and swap with the second element, etc.

Realistically, you wouldn't normally need selection sort because it is
O(n^2) and so is not an optimal solution for large lists.

####<a id="mergesort">Divide and Conquer: merge sort</a>

__Merge sort__ is a recursive algorithm that uses the divide-and-conquer
approach to keep splitting a list in half until there are pairs of
individual items.  Don't worry if the list is an odd number, just add
the last item anywhere.  We then go in reverse (instead of splitting),
we combine the items back together using a __merge__.  This merge
compares the pairs and determines which is smaller/larger, which then
sorts the smaller list.  The trick is that since all of our lists are
already sorted, when we combine pairs of lists, we only need to look at
the first element of each list.

Use mergesort when quicksort is impractical (if you can't have the possibility
of quicksort's worst case runtime of 'O(n^2)'.
Mergesort is also better able to be done in parallel computing.

Here's a chart:

    Algorithm   Time Complexity                         | Space   Complexity
                Best        Average     Worst           | Worst
    Quicksort   Ω(n log(n)) Θ(n log(n)) O(n^2)          | O(log(n))
    Mergesort   Ω(n log(n)) Θ(n log(n)) O(n log(n))     | O(n)

Here is an example run:

    # k     i       j
    # 0 1 2 3 4 5 6 7
    # position 9 7 3 8 4 5 6 2
    # original values in list

    # start splitting up (divide by two) 9 7 3 8 | 4 5 6 2
    # divide into two lists
    9 7 | 3 8 | 4 5 | 6 2

    # divide by two again into four lists
    9 | 7 | 3 | 8 | 4 | 5 | 6 | 2
    # divide until all items are separated

    # now merge back with the merged pairs sorted 7 9 | 3 8 | 4 5 | 2 6
    # merge from single items to pairs that are sorted 3 7 8 9 | 2 4 5 6
    # merge again, key is that we only compare first item from each list (since they are sorted)
    2 3 4 5 6 7 8 9
    # combine again, get final sorted list

For example, if we want to sort a hand of playing cards:

1. __divide__ means splitting the n-element sequence into two subsequences
of n/2 elements each 2. __conquer__ by sorting the two subsequences
recursively using merge sort 3. __combine__ by merging the two sorted
subsequences to produce the sorted answer

Merge sort is good for data that is too big to fit into memory at once
because the pattern of storage is regular.  It is especially good for
data stored as linked lists.

Example Code:

    """ Merge Sort """

    def mergeSort(mylist):
        print "Splitting", mylist

        if len(mylist) > 1:
            mid = len(mylist) // 2 lefthalf = mylist[:mid] righthalf = mylist[mid:]

            mergeSort(lefthalf) mergeSort(righthalf)

            # below code merges the two smaller sorted lists to larger
            sorted list i = 0  # left half index j = 0
            # right half
            index k = 0  # main / large sorted list

            while i < len(lefthalf) and j < len(righthalf):
                # take the smallest value from either left or right half
                if lefthalf[i] < righthalf[j]:
                    mylist[k] = lefthalf[i]  # smaller value on lefthalf
                    i += 1
                else:
                    mylist[k] = righthalf[j]  # smaller value on righthalf
                    j += 1
                k += 1

            # insert remaining values from lefthalf
            while i < len(lefthalf):
                mylist[k] = lefthalf[i] i += 1 k += 1

            # insert remaining values from righthalf
            while j < len(righthalf):
                mylist[k] = righthalf[j] j += 1 k += 1
        print "Merging", mylist

    if __name__ == '__main__':
        mylist = [54,26,93,17,77,31,44,55,20]
        print "Original: ", mylist
        mergeSort(mylist)
        print "Merge Sorted: ", mylist


####<a id="quicksort">Divide and Conquer: quick sort</a>

__Quick sort__ is an efficient algorithm that does a sort _in place_
by splitting the array into two smaller arrays, one with lower value
elements and one with higher value elements based off a 'pivot' element.

Quicksort is my go-to sorting algorithm. This is a really good algorithm
to know offhand. It performs fast in most real-world data (because its inner
loop can be efficiently implemented in most architectures)

Example Run:

     6 5 1 3 8 4 6 9 5  # initial list

    # we pick a pivot (say for this example it is the last element of
    #the list, which is 2; can also be any item, e.g. the first item of the list, median)
    |6 5 1 3 8 4 6 9 5

    # we setup the pivot wall (separated by `|`), 
    # items to the left should be smaller, items to
    # the right of the wall are larger
    |6 5 1 3 8 4 6 9 5
    
    # we compare the pivot element 5 with the first element to the right of the wall (6);
    # 5 < 6 so no swap needed
    |6 5 1 3 8 4 6 9 5

    # we compare the pivot element 5 with the next element to the right of the wall (5);
    #5 == 5 so no swap needed
    1|5 6 3 8 4 6 9 5

    # our current element 1 is smaller than the pivot
    # element 5 so we switch the current element (1) with the lowest
    # index item on the right side of the wall (6)

A good video of quicksort is here: https://www.youtube.com/watch?v=kUon6854joI

It's similar to _merge sort_ in that it does the divide and conquer
approach. The advantage is that you do not need as much storage over
_merge sort_, but the performance could possibly be diminished (depending
on if the __pivot value__ selected is near the middle).

1. Pick an element from the array; this element is called a __pivot__
2. We now want to do a __partition__ operation; this means we want to
reorder the array so that all elements with values less than the pivot
are on one side while all elements with values greater than the pivot
are on the other side (equal values can go either way).  After this
paritioning, the pivot element is in its final position.
3. Partitions begin by locating two position markers (e.g. _leftmark_ and _rightmark_)
at the beginning and end of the remaining items in the list.  The goal of
the partition process is to move items that are on the wrong side with
respect to the pivot value while also converging on the __split point__
(i.e. the actual position where the pivot value belongs in the final sorted list)
4. We increment __leftmark__ until we locate a value that is greater
than the pivot value.  We then decrement __rightmark__ until we locate
a value less than the pivot value.  When this happens, we exchange the
two items and repeate the process again.

Example Code:

    """ Quick Sort in Python3
    Quick sort uses divide and conquer to gain the same advantages as merge sort,
    with the benefit of using less storage, but at the cost of a worse worst case runtime
    O(n^2) if the pivot values are bad.
    """
    import pdb
    from typing import List


    def quickSort(mylist):
        """ Initialize our recursive function """
        quickSortHelper(mylist, 0, len(mylist)-1)

    def quickSortHelper(mylist, first, last):
        """ Recursive function to split up """
        if first < last:  # check if need to sort still

            splitpoint = partition(mylist, first, last)

            # now that we know our splitpoint, we can then recursively run quicksort on the list's bottom half and top half
            quickSortHelper(mylist, first, splitpoint-1)
            quickSortHelper(mylist, splitpoint+1, last)

    def partition(mylist, first, last):
        """ Partition Process, made up of:
        * Pick a pivot value (i.e. what we'll compare our unsorted numbers to)
        Based off this value, we'll compare our unsorted values and either move
        our items to the left of the pivot or to the right of the pivot.
        * """
        pivotvalue = mylist[first]  # get the first value as pivotvalue

        leftmark = first + 1
        rightmark = last

        done = False
        while not done:

            # Go from leftmost side onwards (to right) and try to find a value
            # that is greater than the pivot value (i.e. left side of pivot should be
            # smaller values than pivot value, if we found one that is greater, we
            # stop at leftmark, saying we need to do a swap to the right side)
            while leftmark <= rightmark and mylist[leftmark] <= pivotvalue:
                leftmark += 1

            # Go from rightmost side inwards (to left) and try to find a value
            # that is less than the pivot value (i.e. right side of pivot should be
            # greater values than pivot value, if we found one that is smaller, we
            # stop at rightmark, saying we need to do a swap to the left side)
            while rightmark >= leftmark and mylist[rightmark] >= pivotvalue:
                rightmark -= 1

            if rightmark < leftmark:
                done = True  # we're done sorting through this list because we've crossed
            else:
                # we have a swap between a value in the left list and a value in the right list
                mylist[leftmark], mylist[rightmark] = mylist[rightmark], mylist[leftmark]

        # Once rightmark is less than leftmark, then rightmark is now the split point.
        # That means what we picked as the pivot value can now be exchanged with the 
        # contents of the split point and the pivot value is now in the correct place
        # Note: remember that our pivot value was the first value in our list
        mylist[first], mylist[rightmark] = mylist[rightmark], mylist[first]

        return rightmark


    if __name__ == '__main__':
        mylist = [54, 26, 93, 17, 77, 31, 44, 55, 20]
        print("Original: ", mylist)
        quickSort(mylist)
        print("Quick Sorted: ", mylist)

####<a id="quickselect">Quickselect Algorithm</a>

The __quickselect__ algorithm is a selection algorithm, whereas the __quicksort__
algorithm is a sorting algorithm. It's similar to quicksort except that we
only need to recursively call one side of the partition to be re-partitioned.
Instead of a runtime of `O(log n)`, we get `O(n)`.

What this solves is that if we get an unordered list of items and want to find
the k-th smallest or k-th largest item, we can get items in `O(n)`.

    """ Python 3 implementation of a quickselect algorithm """
    from typing import List
    import unittest
    import random


    class Solution:

        def quickselect(self, items, item_index):
            if items is None or len(items) < 1:
                return None

            if item_index < 0 or item_index > len(items) - 1:
                raise IndexError()

            return self.select(items, 0, len(items) - 1, item_index)

        def select(self, lst, l, r, index):
            # base case
            if r == l:
                return lst[l]

            # choose random pivot
            pivot_index = random.randint(l, r)

            # move pivot to beginning of list
            lst[l], lst[pivot_index] = lst[pivot_index], lst[l]

            # partition
            i = l
            for j in range(l+1, r+1):
                if lst[j] < lst[l]:
                    i += 1
                    lst[i], lst[j] = lst[j], lst[i]

            # move pivot to correct location
            lst[i], lst[l] = lst[l], lst[i]

            # recursively partition one side only
            if index == i:
                return lst[i]
            elif index < i:
                return self.select(lst, l, i-1, index)
            else:
                return self.select(lst, i+1, r, index)


    class SolutionTest(unittest.TestCase):

        def test_quickselect(self):
            s = Solution()
            response = s.quickselect([12, 2, 4, 3, 5], 2)
            assert response == 4

            response = s.quickselect([12, 2, 4, 3, 5], 0)
            assert response == 2


####<a id="heapsort">Divide and Conquer: heap sort</a>

__Heap sort__ takes the best properties of merge sort (the run
time) and the efficency of insertion sort's ability to sort in place
(i.e. only a constant number of elements are stored outside the input
array at any time).  What makes heapsort unique is that it uses a data
structure called a __heap__ to help manage information (instead of a
__linear-time search__); heapsort divides its input into a sorted and
an unsorted region and it iteratively shrinks the unsorted region by
extracting the largest element and moving that to the sorted region.
This heapsort is really efficient at managing __priority queues__.

Really, heapsort is just an implementation of __selection sort__ using
the right data structure.

Note: In programming languages like Java or Lisp, heap refers to
'garbage-collected storage'; this is not what we're talking about here.

Given an array _A_ that represents a heap, we can look at the two
attributes (_length_, _heap-size_) and determine what part is valid
(i.e. this is the correctly sorted region) and what is still the heap
(i.e. unsorted):

*  `A.length` gives us the number of elements in the array *
`A.heap-size` gives us how many elements in the heap are stored within
array _A_.  *  The heap would then be calculated as `A[1 ... A.heap-size]`
where `0 <= A.heap-size <= A.length`

#####<a id="heaps">Heaps</a>

__Heaps__ are a simple data structure for efficiently working with priority queue
operations _insert_ and _extract-min_. They work by maintaining a partial order
on the set of elements that are weaker than the sorted order (i.e. efficient to maintain),
yet stronger than random order (so finding minimum element is quick)

Power in any hierarchically-structured organization is reflected by a tree, where each
node represents a person. Each edge `(x, y)` implies that `x` directly supervises `y`
(aka `x __dominates__ y`). The top person is at the root of the heap / `top of the heap`.

There's a couple variations for a __heap-labeled tree__, where we have a binary tree
that has the key labeling of each node __dominating__ the key labeling of each of its
children.

* A __min-heap__ is where a node dominates its children by having a smaller key than the children
* A __max-heap__ is where a node dominates its children by having a larger key than the children

A binary tree might look like:

                     1492
                  /        \
               1783        1776
              /     \     /    \
            1804   1865 1945  1963
            /
          1918

This binary tree might be represented as a node with points to its two children. However,
the memory used by the pointers can easily outweight the size of keys.

The __heap__ is a data structure that will let us represent binary trees without using any
pointers. We store data as an array of keys, and the important difference is that we will use
the position of the keys to implicitly satisfy the role of the pointers.

We store the root of the tree in the first position of the array. In the second position,
we have the left child. On the third position, we have the right child. The positions can
actually be calculated, where given an array `k`, the __left children__ of `k` will sit
at positoin `2k` and the __right children__ will be `2k+1` while the parent of `k` will be
in position `k/2`. We can then move around without any pointers.

    1   1492
    2   1783
    3   1776
    4   1804
    5   1865
    6   1945
    7   1963
    8   1918

####<a id="heapissues">Heap Issues</a>

Space

We can store any binary tree in an array without pointers, but the issue is that we might
get a tree that is __sparse__. If we were given a tree with height `h` that was sparse (number
of nodes `n < 2^h`, then all missing internal nodes still take up space in our structure.
We need to represent the full binary tree to maintain the positional mapping between parents
and children. Space efficiency demands that we not allow holes in our tree (i.e. each level 
be packed as much as it can be)

Search for a particular key

We can't search for a particular key in a heap beause we don't know anything about the relative
order of the n/2 leaf elements in a heap. We have to do linear search through them.
In order to do a binary search (i.e. we have a sorted list, then divide by 2 to see if we're
looking at the upper or lower, etc), we need a binary search tree. We're missing the order of
elements in the heap.

####<a id="constructingaheap">Constructing a Heap</a>

Heaps are constructed incrementally, by inserting each new element into the left-most open
spot in the array, namely the (n+1)st position of a previously n-element heap. We get a
balanced shape out of a heap-labeled tree, but note that we don't necessarily maintain the
dominance ordering of the keys.

The new key might be less than its parent in a min-heap or greater than its parent in 
a max-heap. We'll need to swap any dissatisfied element with its parent. We do this process
repeatedly, essentially _bubbling up_ the new key to its right position. The swap process
takes constant time. With an `n` element heap, we have a height of `lg n`, which makes each 
insertion take at most `O(log n)` time. That means an initial construction of the heap
takes `O(n log n)` time.

We can make faster heaps using the `bubble_down` procedure, which will create heaps
in linear time instead of `O (n log n)` time. However, also note that construction time
does not help improve worst-case performance.

####<a id="heapextractminimum">Extracting the Minimum from a Heap</a>

We can identify the dominant element of a heap since it sits at the top of the heap
in the first positoin of the array. Removing the top element leaves a hole in the array.
We can fill the hole by moving the element from the _right-most_ leaf (at the `n`th position
of the array) into the first position.

Heapify with `bubble_down`

The shape of the tree is restored, however the labeling of the root might not satisfy the
heap property (e.g. if min-heap, key might be less than its parent / i.e. the new root may be
dominated by its both of its children). We can swap by _bubbling down_ the heap until the
element dominates all its children; this percolate-down operation is called __heapify__
because it merges two heaps (the subtrees below the original root) with a new key.

####<a id="heapsort">Heapsort</a>

When we exchange the maximum element with the last element and call heapify repeatedly,
we get an `O (n log n)` sorting algorithm called __Heapsort__. Heapsort is easy to program,
runs an _in-place_ sort (i.e. uses no extra memory) and has a worst-case of `O(n log n)` time,
which is the best that you can expect from any sorting algorithm.

There are some other algorithms that are slightly faster in practice, but you can't go wrong
with heapsort for sorting data that sits in your computer's memory.

####<a id="binaryheap">(Binary) Heap</a>

A __(binary) heap__ data structure is an array object that we can view
as a binary tree.  Think of this algorithm as two parts:

1. We have some data (e.g. a list of `[6, 5, 3, 1, 8, 7, 2, 4]`) that we
use to create the _heap_, a data structure that looks like a binary tree.

A __heap__ is a complete binary tree in which the node is less than all
the values in its subtrees (or greater if reversed). An example looks like:

               3
              /  \
            9     4
           / \   / \
          26 10 18 20
          
As we're building this binary tree, the heap swaps elements depending
on the type (e.g. min or max) of the binary heap (e.g. sorting smallest
to largest, larger nodes don't stay below smaller node parents and end
up swapping; `8` can't be below `5` on the heap).  Once the binary tree
is built, we have a tree where each array index represents a node and
also has the index of the node's parent, left child branch, or right
child branch.

2. We then create a _sorted array_ by repeatedly removing
the largest element from the root of the heap and inserting it into
the array.  The heap is updated after each removal to maintain the heap.
The heap incrementally gets smaller until all objects have been removed
from the heap, resulting in only a sorted array left.

####<a id="priorityqueue">Priority Queue</a>

As mentioned earlier, __heap sort__ is great for creating __priority
queues__, which is a data structure for maintaining a set _S_ of elements,
each with an associated value called a __key__.  There's __max-priority
queue__ (e.g. used to schedule jobs on a server and set their relative
priorities) and a __min-priority queue__ (e.g. used for _discrete
event-driven simulation_ models like determining how many patients can
be served from changing 8 hours to 9 hours of operation when avg surgery
takes say 4 hours).

##<a id="decisiontree">Sorting Algorithms with Linear Time (Decision
Tree Models)</a>

Previously mentioned algorithms are __comparison sorts__, which determines
the sort order based only on comparisons between the input elements.
If we make the assumption that all the input elements are distinct, we can
sort by __linear time__.  This means we can do comparison sorts in terms
of __decision trees__, which is a full binary tree that represents the
comparisons between elements in a sorting algorithm (say elements `i` :
`j`).

####<a id="countingsort">Counting Sort</a>

Placeholder for smart stuff.

####<a id="radixsort">Radix Sort</a>

Placeholder for smart stuff.

####<a id="bucketsort">Bucket Sort</a>

Placeholder for smart stuff.

##<a id="probabilisticanalysis">Probabilistic Analysis</a>

__Probabilistic analysis__ is the use of probability in the analysis of
problems.  We can use this in analyzing the running time of an algorithm
or we can use it to analyze other quantities, such as who to hire.
We have some examples:

*  Determine the probability that in a room of k people, two of them
share the same birthday.  *  What happens when we randomly toss balls into
bins *  Where 'streaks' of consecutive heads come from when we flip coins

####<a id="hiringproblem">The Hiring Problem</a>

For this example, we want to hire an office assistant.  We interview
candidates and determine if they are better than the current assistant
(if so, replace the current assistant right then and there).  There is
a cost to hiring and firing someone.  We want to find the expected cost
after interviewing everyone (which is a fixed n candidates).

Say we have a list and rank them into an ordered list of best
possible candidate using: `[rank1, rank2, rank3, rankn]`.  Saying that
applicants come in a random order is equivalent to saying there is __n!__
permutations of the numbers 1 through n.  We call this __uniform random
permutation__, which means that each of the possible n! permutations
appears with equal probability.

We first assume (or make sure we randomly select) candidates for hire.
We can check probabilities and expectations using an __indicator random
variable__.  For example, if we flip a coin, we count the number of times
it actually comes up heads (saying using a __random number generator__)
to what we expect.

####<a id="birthdayparadox">The Birthday Paradox</a>

How many people must there be in a room before there is a 50% chance
that two of them are born on the same day of the year?  We have:

*  `k = number of people in the room`; we index the people in the room
with integers (e.g. 1, 2, 3, ... k) *  `n = 365 days`; we ignore leap
years *  Assume that birthdays are uniformly distributed across n days
and are independent.

####<a id="ballsbins">Balls and Bins</a>

If we randomly toss identical balls into 'b' bins (1 through 'b') and
assuming the tosses are independent with an equal chance of ending up in
any bin, we have the probability that a tossed ball lands in any given
bin as `1/b` of success (where success is falling into the given bin).
The ball tossing can be seen as a sequence of __Bernoulli trials__
(i.e. a binomial trial, a random experiment where there are exactly two
possible outcomes; success and failure).  This answers questions like:

*  How many balls fall in a given bin?  *  How many balls must we toss,
on average, until a given bin contains a ball?  *  How many balls must
we toss until every bin contains at least one ball?  (aka the __coupon
collector's problem__, which says that a person trying to collect each
of _b_ different coupons expects to acquire aprpoximately _b_ ln _b_
randomly obtained coupons in order to succeed).

####<a id="streaks">Streaks</a>

If you flip a fair coin 'n' times, what is the longest streak of
consecutive heads that you expect to see?

##<a id="graphtheory">Graph Theory</a>

__Graph theory__ is the study of graphs, which are mathematical structures
used to model pairwise relations between objects.  A __graph__ is made
up of __vertices__ (aka __nodes__; note that a node is called a __leaf__
if it has no children) and lines that connect them are called __edges__.

###<a id="graphalgorithms">Famous Graph Algorithms</a>

####<a id="dijkstra">Dijkstra's Shortest Path</a>

__Dijkstra's shortest path__ algorithm is commonly used to find the 
the shortest paths on a graph from a source to all vertices.

####<a id="astar">`A*` search algorithm</a>

The `A*` (__A Star__) algorithm is used in pathfinding and graph traversal,
which is the process of finding a path between multiple points (aka __nodes__).
Its used due to its performance and accuracy, but in practical travel-routing systems,
it is generally outperformed by algorithms which can pre-process the graph to
attain better performance.

####<a id="prims">Prim's Algorithm for Minimum Spanning Tree</a>

__Prim's Algorithm for Minimum Spanning Tree__ is a greedy algorithm that 
takes a spanning tree (all vertices must be connected) where the two 
disjoint subsets of vertcies are connected to make a Spanning Tree.

TODO: How this algorithm works

####<a id="trees">Trees</a>

__Trees__ are a type of graph and they're described as: *  Each tree
has a __root node__ *  The root node has zero or more child nodes *
Each child node has zero or more child nodes, and so on.

The tree cannot contain cycles.  The nodes may or may not be in a
particular order, they could have any data type as values, and they may
or may not have links back to their parent nodes.

__Real life examples of trees__

*  A web site
    -  Starts at `<html>` -  Next level has `<head>` node and `<body>` node
    -  `<head>` splits into `<meta>` and `<title>`; `<body>` splits into `<h1>` and `<h2>`
*  File system on a computer (e.g. UNIX)
    -  Starts at `/` -  Next level has `dev/`, `etc/`, `usr/`, `var/`, etc.

__Pieces of Trees__

*  __Node__ has a name (the __key__) and can also contain additional
information (the __payload__).  The highest node is the __root node__,
which has no parent.
*  __Edge__ - an edge connects two nodes to show that there is a
relationship between them.  Every node except the root node has one
incoming edge from another node.  Each node can have several
outgoing edges.
*  __Path__ - a path is an ordered list of nodes that are connected by edges
    E.g. `C:` -> `Users\` -> `WLiu` -> `My Documents` *
__Parent__ - node A is the parent node of B if A has outgoing edges that
connect to B.
*  __Children__ - node B is a child node of node A if B has incoming
edges from A.
*  __Siblings__ - node C and B are siblings if they share the same
parent node A.
*  __Subtree__ - A subtree is a set of nodes and edges with parent A and all
    the descendants of that parent.
* __Leaf Node__ - A leaf node is a node that has no children.
*  __Level__ - the level of a node n is the number of edges on the path from the
root node to n.  The level of the root node is zero.
*  __Height__ - the height of a tree is the maximum level of any node in the tree.

__How to appraoch trees__

We can think of trees two different ways, as a large set of rules or as
a smaller recursive set of rules:

1. A tree consists of a set of nodes and a set of edges that connect
pairs of nodes.  Trees have the following properties:
    -  One node of the tree is designed as the root node -  Every node
    _n_, except the _root node_, is connected by an edge from exactly
    one other node _p_, where _p_ is the parent of _n_.
2. Think recurisvely; a tree is either empty or consists of a root and
zero or more subtrees, each of which is also a tree.

__Types of Trees__ There are different types of trees that can take on
different properties:

*  We can describe a tree as a __binary tree__ (a node has up to 2
children), __ternary tree__ (a node has up to 3 children) and so forth.
*  A __binary tree__ means each node has up to 2 children vs a __binary
search tree__, which has the _additional_ requirement that `all left
descendants <= n < all right descendents` for every node.  Note that
some definitions of a binary search tree cannot have duplicates, others
the duplicate values will be on the right or can be on either side.
All are valid definitions.
*  __balanced__ vs __unbalanced__ does not mean that the left aznd right 
subtrees are exactly the same size (like a _perfect binary tree_).
It simply means that they're balanced enough to ensure `O(log n)` run 
times for `insert` and `find` operations.
*  A __complete binary tree__ is a binary tree where every level of the tree is
fully filled, except for possibly the last level (filled left to right).
Examples of these include __min binary heaps__ and __max binary heaps__.
*  A __full binary tree__ is a binary tree in which every node has either
zero or two children (no nodes have only one child) *  A __perfect binary
tree__ is a tree that is both full and complete.  All leaf nodes will
be at the same level and this level has the maximum number of nodes.

__Traversing Trees__ You can traverse binary trees using a variety of
methods (the most common being 'in-order' traversal):

*  __pre-order traversal__ means to visit the current node before its
child nodes (thus the name, pre-order).  The root is always the first
node visited.  *  __in-order traversal__ means to visit the left branch,
then the current node, and finally the right branch.  When performed on
a binary search tree, it visits the nodes in ascending order (thus the
name, in-order) *  __post-order traversal__ means to visit the current
node after its child nodes (thus the name, post-order).  The root is
always the last node visited.

__Tries (prefix trees)__

A __trie__ (aka __prefix tree__) is a special kind of tree; a trie is
like an n-ry tree in which characters are stored at each node and each
path down the tree may represent a word.

There are `*` nodes (aka __null nodes__) that are used to indicate
complete words (e.g. `Many*` means many is a complete word or `Ma*`
means there are lots of words that start with `Ma`).

A trie is commonly used to store the entire English language for quick
prefix lookups.  A hash table can quickly look up whether a string
is a valid word, but it cannot tell us if a string is a prefix of any
valid words.

#####<a id="typesoftrees">Types of Trees</a>

We have different types of trees including:

* __Binary Tree__
* __Binary Search Tree__
* __AVL Tree__
* __Heaps__

####<a id="exampletrees">Example Trees</a>

An example tree belongs to the category directed acyclic graphs (DAGs).
Git is a great example of a DAG. Other examples are say an Airflow scheduled job.

####<a id="graphs">Graphs</a>

A tree is a type of __graph__, but not all graphs are trees.  A tree
is a connected graph without cycles.  A graph is a collection of nodes
with edges between (some of) them.  Graphs are used to model pairwise
relations between objects.

*  graphs can be either __directed__ (where edges are like a one way
street) or __undirected__ (where edges are like a two way street).  *
graphs might consist of multiple isolated subgraphs.  *  if there is a
path between every pair of vertices, it is a __connected graph__ *  graphs
can (or not) have cycles.  an __acyclic graph__ is a graph without cycles

####<a id="rlexamplegraphs">Real Life examples of Graphs</a>

Computers can use graphs to find the shortest, quickest, or easiest path
from one place to another.  Some real life scenarios include:

*  systems of roads *  airline flights from city to city *  how the
internet is connected *  sequence of classes you must take to complete
your college major

####<a id="classicexamplegraphs">Classic examples of Graphs</a>

*  The 'Word Ladder Problem' puzzle by Lewis Carroll where you change a
word into another word, can only change one letter at a time and each
step you transform the word must still be another word (e.g. FOOL >
POOL > POLL >... > SAGE) *  The 'Knights Tour Problem' is a puzzle on
the chess board with a single chess piece (the knight).  The object of
the puzzle is to find a sequence of moves that allow the knight to visit
every square on the board exactly once.  One sequence is a 'tour'.
    -  Each square on the chessboard is represented as a node in the
    graph.  -  Each legal move by the knight is represented as an edge

####<a id="vocabdefinegraphs">Vocabulary and Definitions for Graphs</a>

A graph can be represented by `G=(V,E)` for the graph `G`, `V` is a set
of vertices and `E` is a set of edges.  Each edge is a tuple `(v, w)`
where `w, v` makes up `V`.  Optionally, we can add a third component to
the edge tuple to represent a weight.

*  __vertex__ (aka a __node__) has a name (the __key__) with additional
    information (the __payload__).
*  __edge__ (aka an __arc__) connects two vertices to show their
    relationship. Edges can be __one-way__ (creating a __directed graph__,
    aka __digraph__) or __two-way__ (creating an __undirected graph__).
    -  When two 'vertices' are connected by an edge, they are
    __adjacent__.
    -  Example of a 'directed graph' is if Person A knows Person B,
       but Person B does not necessarily know Person A
    -  Example of an 'undirected graph' is if Person A shakes hands with Person B,
        Person B has also shaken hands with Person A
*  __weight__ is the cost to go from one vertex to another.
    -  Example is a graph of roads that connect one city to another;
    the weight on the edge might represent the distance between the
    two cities.
*  __Path__ in a graph is a sequence of vertices that are connected by edges.
*  __Cycle__ in a directed graph is a path that starts and ends at the same vertex.
    -  A graph with no cycles is an __acyclic graph__.
    -  A 'directed graph' with no cycles is called a __directed acyclic graph__ (aka
    a __DAG__).

####<a id="graphabstractdatatype">Graph Abstract Data Type (ADT)</a>

This is how to setup a graph abstract data type (ADT) that we can build
off of:

*  `Graph()` creates a new, empty graph *  `addVertex(vert)` adds
an instance of `Vertex` to the graph *  `addEdge(fromVert, toVert)`
adds a new, directed edge to the graph that connects two vertices *
`addEdge(fromVert, toVert), weight)` adds a new, weighted, directed edge
to the graph that connects two vertices *  `getVertex(vertKey)` finds the
vertex in the graph named `vertKey` *  `getVertices()` returns a list
of all vertices in the graph *  `in` returns `True` for the statement:
`vertex in graph` if the given vertex is in the graph, `False` otherwise


####<a id="storegraphs">Ways to store a graph (adjacency list and
matrix)</a>

You can store graphs as an __adjacency list__ (most common way) or as an
__adjacency matrices__.  When two verticies are connected by an edge,
they are __adjacent__, thus the name of list and matrix.  There are
advantages and disadvantages to both.

*  an __adjacency matrix__ is a two-dimensional matrix, more precisely
a `N*N` boolean matrix (where N is the number of nodes).  A value at
`matrix[v][w]` means row `v` and column `w`, which indicates an edge
from node v to node w.
    -  Advantages: The adjacency matrix is simple, especially for small
    graphs where you can see which nodes are connected to other nodes.
    -  Disadvantages: Usually searches on an adjacency matrix are less
    efficient than an adjacency list since you have to search through
    all the nodes to identify a node's neighbors.  Since most of the
    cells are usually empty (i.e. a __sparse matrix__), this is not an
    efficient way to store data.  It is rare to see a real world problem
    where most vertexes connect to most other vertexes.

__Example adjacency matrix__

        v0  v1  v2  v3  v4  v5
    v0       5               2
    v1           4
    v2               9
    v3                   7   3
    v4   1
    v5           1       8

*  an __adjacency list__ implementation involves keeping a master list of
all the vertices in the Graph object and then each vertex object in the
graph maintains a list of the other vertices that it is connected to.
We can implement the `Vertex` class as a dictionary or a list (below
sample is as a dict where the keys are the vertices and the values are
the weights)
    -  Advtanges:  More space-efficient to implement a sparesely
    connected graph.

__Example adjacency list__

    v0: id=v0, adj={v1:5, v5:2}
    v1: id=v1, adj={v2:4}
    v2: id=v2, adj={v3:9}
    v3: id=v3, adj={v4:7, v5:3}
    v4: id=v4, adj={v0:1}
    v5: id=v5, adj={v2:1, v4:8}

####<a id="adjacencylistimplement">Adjacency List Implementation</a>

To implement an 'adjacency list', we need to implement:

*  `Graph`, which is a master list of the vertices; this contains a
dictionary that maps vertex names to vertex objects.  *  `Vertex`, which
represents each vertex in the graph.  Each vertex uses a dictionary to
keep track of the vertices to which it is connected and the weight of
each edge.


####<a id="searchgraphs">Ways to search a graph</a>

After constructing a graph.  The two most common ways to search a graph
are __depth-first search__ and __breadth-first search__.

*  In __depth-first search (DFS)__, we start at the root (or an
arbitrarily selected node) and explore each branch completely before
moving on to the next branch (thus the name depth-first) before we
go wide.  This is usually a simpler approach.

*  In __breadth-first search (BFS)__, we start at the root (or an
arbitrarily selected node) and explore each edge/neighbor before going on
to any of their children (thus the name breadth-first) before we go deep.
To keep track of which vertices have been visited, we color them to:
    -  `white` - all vertices are initialized as white when constructed;
    this means the vertex is undiscovered (i.e. not visited yet)
    -  `gray` - when a vertex is initially discovered it is colored gray.  When a
    vertex is colored gray, there may be some white vertices adjacent to
    it (indicating that there are still additional vertices to explore)
    - `black` - when a vertex is completely explored, it is colored black.
    Once a vertex is black, there are no white vertices adjacent to it.

The heart of a __BFS__ is a `Queue`, which decides which vertex to
explore next.

####<a id="treesvsgraphs">Trees vs Graphs</a>

You can think of a __tree__ as just a restricted form of a __graph__.
The main difference is that a tree is setup so that each node has only 
one parent, but graphs can have multiple predecessors (i.e. we don't
say 'parent' for graphs because there can be multiple connections)
Trees have a root node while graphs don't.

Trees also don't contain cycles. Trees fit into a category called __Directed Acyclic Graphs__ (DAG),
meaning that there are no cycles (i.e. acyclic). Graphs can be cyclic or acyclic (remember
that a tree is always a graph, but a graph is not always a tree).

An example of a tree would be a scheduled job that depended on previous jobs.
An example of a graph would be a map with cities being connected.

##<a id="npcompleteproblems">NP-Complete Problems</a>

A __NP-Complete__ problem stands for __nondeterministic polynomial time__ problem.
There is no known way to find a solution quickly (i.e. the time required to solve
a problem using any currently known algorithm increases rapidly as the size of the
problem grows). Note: By quickly, it means in __polynomial time__.

Some famous NP-Complete problems are:

* Traveling Salesman
* Knapsack

###<a id="nphardproblems">NP-Hard Problems</a>

A __NP-Hard__ problem is one that is not solvable in polynomial time, but can be
verified in polynomial time.

##<a id="gotchas">Gotchas</a>

Remember when whiteboarding, its a bit different than coding because you can't just
run your code. Some things to watch out for include:

* Off by 1 - (e.g. `range(10)` will be `0` to `9`, but not 10)
* 
