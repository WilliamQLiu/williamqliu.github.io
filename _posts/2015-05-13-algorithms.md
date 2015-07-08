---
layout: post
title: Algorithms
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
    - [Structure of Data](#datastructure)
    - [What is an Algorithm](#whatisalgorithm)
    - [Is the algorithm correct?](#algorithmcorrect)
    - [Is the algorithm efficient?](#algorithmefficient)
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
    - [Graphs](#graphs)
    - [Trees](#trees)

TODO:

*  [Data Structures](#datastructures)
*  [Algorithm Techniques](#algorithmtechnique)
*  [Graph Theory](#graphtheory)
*  [Greedy Methods](#greedy)
*  [Dynamic Programming](#dynamicprogramming)
*  [NP complete](#npcomplete)
*  [Parallelism](#parallelism)


##<a id="summary">Summary</a>

####<a id="datastructure">Structure of Data</a>

An individual data piece (i.e. a __record__) is usually part of a bigger collection of data.  Each record has a __key__ (the value to be sorted) and the rest of the record has __satellite data__ (data carried around with the key).

So what goes on behind the scenes?  When we move data around, we apply a sorting algorithm; usually there's a large amount of satellite data that makes it slow to physically move (e.g. erase from hard drive, write to another place in hard drive) so instead we normally just move our pointer to the records.

A __sorting algorithm__ is the method that we determine the sorted order (regardless of individual fields or large satellite data).

####<a id="whatisalgorithm">What is an Algorithm?</a>

So let's take a step back.  An __algorithm__ is a sequence of steps that takes an __input__ (i.e. some value(s)) and produces an __output__ (some value(s)).  For example, we might encounter a _sorting problem_ where we want to sort a sequence of numbers into nondecreasing order.  Most of these notes below are taken from reading the book 'Introduction to Algorithms' by Thome H. Cormen.

####<a id="algorithmcorrect">Is the algorithm correct?</a>

An algorithm is correct if _every_ input instance halts with the correct output.  A single input is an __instance of a problem__.  For example:

    // an instance of a problem
    Input Sequence of {31, 41, 59, 26, 41, 58}
    Output Sequence of {26, 31, 41, 41, 58, 59}  

####<a id="algorithmefficient">Is the algorithm efficient?</a>

There are different algorithms to solve the same problem and they are often drastically different in efficiency.  We normally measure efficiency by looking at _running time_, which is mainly affected by the _size of input_.

1. the _size of input_ (i.e. normally we look at the _number of items in the input_; for other problems like multiplying integers, we look at the _total number of bits_ used)
2. the _running time_ (i.e. the number of 'steps'; the number of times something is executed).  We normally focus on the _worst-case running time_ (aka __Omega__), but sometimes we look at the _average-case running time_ (__Theta__) when doing _probabilistic analysis_.  We rarely care about _best-case running time_ (aka __O__).

Other considerations in real life (that we won't consider for now) are things like space (RAM, hard drive), bandwidth speed, caches, and parallelism (single, multiple cores).

##<a id="algorithmdesign">Designing Algorithms</a>

We briefly cover the structure of data, then go into a couple of design approaches with __incremental__ and __divide and conquer__, which are opposites of each other.

*  __incremental approach__ (aka __iteration__) is used in algorithms like _insertion sort_.  This means working with _iterables_, objects that can be used in `for` or `while` loops.
*  __divide and conquer approach__ breaks the problem into several subproblems that are similar to the original problem, but smaller in size.  This is used in algorithms like _merge sort_.

####<a id="incremental">Approach: Incremental</a>

__Incremental__ is the repetition of a block of statements.  An example is:

    a = 0
    for i in range(10):  #0, 1, 2...8, 9
        a+=1
    print a  #10

####<a id="loopinvariant">loop invariant</a>

As we create our loops, we need to be aware of __loop invariants__ (aka __invariant__), which simply means that these general conditions have to be true.

1. __initialization__ means it is true before the first iteration of the loop
2. __maintenance__ means it remains true before the next iteration
3. __termination__ means when the loop terminates, the invariant gives us a useful property that helps show that the algorithm is correct.

####<a id="divideandconquer">Approach: Divide and Conquer</a>

The __divide and conquer__ approach is to break apart a large problem into several subproblems that are similar to the original problem but smaller in size.  We solve the subproblems __recursively__ (i.e. they call themselves) until they reach the __base case__, and then combine these solutions to create a solution to the original problem.

1. __divide__ means to split the problem into subproblems that are smaller instances of the same problem.
2. __conquer__ means solving the subproblems recursively.  If the subproblem size is small enough, just solve the subproblems in a straightforward manner.
3. __combine__ means to combine the subproblem solutions into the solution for the original problem.

####<a id="recursion">recursion</a>

__Recursion__ is a method where the solution to a problem depends on solutions to smaller instances of the same problem.  An example is calculating the Fibonacci number.  You can visually see this as a __recursion tree__, which is a tree diagram of recursive calls.

    def fib(n):
        """ return the Fibonacci number """
        if n==0:
            return 0
        elif n==1:
            return 1
        else:
            return fib(n-1) + fib(n-2)

##<a id="comparisonsortalgorithms">Sorting Algorithms with Comparison Sorts</a>

A basic computational problem is the __sorting problem__, where you sort a sequence of _n_ numbers (aka __keys__).  We apply the above general approaches (_insertion_, _divide and conquer_) using different types of algorithms.  The following algorithms (_insertion sort_, _bubble sort_, _merge sort_, _heapsort_, and _quicksort_) are all __comparison sorts__ (i.e. they determine the order of an input array by comparing elements).

####<a id="insertionsort">Incremental: insertion sort</a>

__Insertion sort__ is a simple sorting algorithm based on the incremental approach and is efficient at sorting a small number of elements in place.  For example, if we want to sort a hand of playing cards:

1. Start with an empty left hand and all cards face down on the table
2. We remove one card from the table and insert it into the correct position on the left hand
3. To find the correct position, we compare it with each of the cards already in the left hand (from right to left); this way left hand cards are always sorted

####<a id="bubblesort">Incremental: bubble sort</a>

__Bubble sort__  (aka __sinking sort__, __ripple sort__) is a simple but inefficient sorting algorithm that repeatedly goes through the list to be sorted, compares each pair of adjacent items, and swaps them if they are in the wrong order.  For example, say we were sorting scrabble tiles into alphabetical order.

1. Place letters on tile holder and look at the first block.
2. Look at the block to the right of it.
3. If the block to the right should come before the block on the left, swap them.
4. Compare the next block in line with the first and repeat step 3
5. Begin step 1 again with the second block

The name bubble sort is because elements tend to move up into the correct order like bubbles rising to the surface and you see a rippling effect for the ones that are not in the correct order.

####<a id="mergesort">Divide and Conquer: merge sort</a>

__Merge sort__ uses the divide-and-conquer approach until the sorted sequence has a length of 1, then merges all the sorted sequences together.  Depending on the length of items, this approach usually has a faster _worst-case running time_ than insertion sort.  For example, if we want to sort a hand of playing cards:

1. __divide__ means splitting the n-element sequence into two subsequences of n/2 elements each
2. __conquer__ by sorting the two subsequences recursively using merge sort
3. __combine__ by merging the two sorted subsequences to produce the sorted answer

Merge sort is good for data that is too big to fit into memory at once because the pattern of storage is regular.  It is especially good for data stored as linked lists.

####<a id="quicksort">Divide and Conquer: quick sort</a>

__Quick sort__ is an efficient algorithm that does a sort 'in place' by splitting the array into two smaller arrays, one with low elements and one with high elements based off a 'pivot' element.

1. Pick an element from the array; this element is called a __pivot__
2. We now do a __partition__ operation; this means we reorder the array so that all elements with values less than the pivot are on one side while all elements with values greater than the pivot are on the other side (equal values can go either way).  After this paritioning, the pivot element is in its final position.
3. Recursively apply the above steps to the sub-array of elements with smaller values and separately to the sub-array of elements with greater values.

####<a id="heapsort">Divide and Conquer: heap sort</a>

__Heap sort__ takes the best properties of merge sort (the run time) and the efficency of insertion sort's ability to sort in place (i.e. only a constant number of elements are stored outside the input array at any time).  What makes heapsort unique is that it uses a data structure called a __heap__ to help manage information (instead of a __linear-time search__); heapsort divides its input into a sorted and an unsorted region and it iteratively shrinks the unsorted region by extracting the largest element and moving that to the sorted region.  This heapsort is really efficient at managing __priority queues__.

Note: In programming languages like Java or Lisp, heap refers to 'garbage-collected storage'; this is not what we're talking about here.

Given an array _A_ that represents a heap, we can look at the two attributes (_length_, _heap-size_) and determine what part is valid (i.e. this is the correctly sorted region) and what is still the heap (i.e. unsorted):

*  `A.length` gives us the number of elements in the array
*  `A.heap-size` gives us how many elements in the heap are stored within array _A_.
*  The heap would then be calculated as `A[1 ... A.heap-size]` where `0 <= A.heap-size <= A.length`

<a id="binaryheap">__(Binary) Heap__</a>

A __(binary) heap__ data structure is an array object that we can view as a binary tree.  Think of this algorithm as two parts:

1. We have some data (e.g. a list of `[6, 5, 3, 1, 8, 7, 2, 4]`) that we use to create the _heap_, a data structure that looks like a binary tree.  As we're building this binary tree, the heap swaps elements depending on the type (e.g. min or max) of the binary heap (e.g. sorting smallest to largest, larger nodes don't stay below smaller node parents and end up swapping; `8` can't be below `5` on the heap).  Once the binary tree is built, we have a tree where each array index represents a node and also has the index of the node's parent, left child branch, or right child branch.
2. We then create a _sorted array_ by repeatedly removing the largest element from the root of the heap and inserting it into the array.  The heap is updated after each removal to maintain the heap.  The heap incrementally gets smaller until all objects have been removed from the heap, resulting in only a sorted array left.

<a id="priorityqueue">__Priority Queue__</a>

As mentioned earlier, __heap sort__ is great for creating __priority queues__, which is a data structure for maintaining a set _S_ of elements, each with an associated value called a __key__.  There's __max-priority queue__ (e.g. used to schedule jobs on a server and set their relative priorities) and a __min-priority queue__ (e.g. used for _discrete event-driven simulation_ models like determining how many patients can be served from changing 8 hours to 9 hours of operation when avg surguery takes say 4 hours).

##<a id="decisiontree">Sorting Algorithms with Linear Time (Decision Tree Models)</a>

Previously mentioned algorithms are __comparison sorts__, which determines the sort order based only on comparisons between the input elements.  If we make the assumption that all the input elements are distinct, we can sort by __linear time__.  This means we can do comparison sorts in terms of __decision trees__, which is a full binary tree that represents the comparisons between elements in a sorting algorithm (say elements `i` : `j`).  

####<a id="countingsort">Counting Sort</a>

Placeholder for smart stuff.

####<a id="radixsort">Radix Sort</a>

Placeholder for smart stuff.

####<a id="bucketsort">Bucket Sort</a>

Placeholder for smart stuff.

##<a id="growthfunctions">Growth of Functions</a>

We are interested in the __asymptotic__ efficiency of algorithms, which means we are estimating for very large inputs.  Usually an asymptotically efficient algorithm will be the best choice for everything but the smallest input cases.

####<a id="bigo">Big O Notation</a>

We use __Big O notation__ to give an estimated running time based on the input size (as it tends toward infinity)

##<a id="probabilisticanalysis">Probabilistic Analysis</a>

__Probabilistic analysis__ is the use of probability in the analysis of problems.  We can use this in analyzing the running time of an algorithm or we can use it to analyze other quantities, such as who to hire.  We have some examples:

*  Determine the probability that in a room of k people, two of them share the same birthday.
*  What happens when we randomly toss balls into bins
*  Where 'streaks' of consecutive heads come from when we flip coins

####<a id="hiringproblem">The Hiring Problem</a>

For this example, we want to hire an office assistant.  We interview candidates and determine if they are better than the current assistant (if so, replace the current assistant right then and there).  There is a cost to hiring and firing someone.  We want to find the expected cost after interviewing everyone (which is a fixed n candidates).

Say we have a list and rank them into an ordered list of best possible candidate using: `[rank1, rank2, rank3, rankn]`.  Saying that applicants come in a random order is equivalent to saying there is _n!__ permutations of the numbers 1 through n.  We call this __uniform random permutation__, which means that each of the possible n! permutations appears with equal probability.

We first assume (or make sure we randomly select) candidates for hire.  We can check probabilities and expectations using an __indicator random variable__.  For example, if we flip a coin, we count the number of times it actually comes up heads (saying using a __random number generator__) to what we expect.

####<a id="birthdayparadox">The Birthday Paradox</a>

How many people must there be in a room before there is a 50% chance that two of them are born on the same day of the year?  We have:

*  `k = number of people in the room`; we index the people in the room with integers (e.g. 1, 2, 3, ... k)
*  `n = 365 days`; we ignore leap years
*  Assume that birthdays are uniformly distributed across n days and are independent.

####<a id="ballsbins">Balls and Bins</a>

If we randomly toss identical balls into _b_ bins (1 through _b_) and assuming the tosses are independent with an equal chance of ending up in any bin, we have the probability that a tossed ball lands in any given bin as `1/b` of success (where success is falling into the given bin).  The ball tossing can be seen as a sequence of __Bernoulli trials__ (i.e. a binomial trial, a random experiment where there are exactly two possible outcomes; success and failure).  This answers questions like:

*  How many balls fall in a given bin?
*  How many balls must we toss, on average, until a given bin contains a ball?
*  How many balls must we toss until every bin contains at least one ball?  (aka the __coupon collector's problem__, which says that a person trying to collect each of _b_ different coupons expects to acquire aprpoximately _b_ ln _b_ randomly obtained coupons in order to succeed).

####<a id="streaks">Streaks</a>

If you flip a fair coin _n_ times, what is the longest streak of consecutive heads that you expect to see?

##<a id="graphtheory">Graph Theory</a>

__Graph theory__ is the study of graphs, which are mathematical structures used to model pairwise relations between objects.  A __graph__ is made up of __vertices__ (aka __nodes__; note that a node is called a __leaf__ if it has no children) and lines that connect them are called __edges__.

####<a id="trees">Trees</a>

__Trees__ are a type of graph and they're described as:
*  Each tree has a root node
*  The root node has zero or more child nodes
*  Each child node has zero or more child nodes, and so on.

The tree cannot contain cycles.  The nodes may or may not be in a particular order, they could have any data type as values, and they may or may not have links back to their parent nodes.

__Types of Trees__
There are different types of trees that can take on different properties:

*  We can describe a tree as a __binary tree__ (a node has up to 2 children), __ternary tree__ (a node has up to 3 children) and so forth.
*  A __binary tree__ means each node has up to 2 children vs a __binary search tree__, which has the _additional_ requirement that `all left descendants <= n < all right descendents` for every node.  Note that some definitions of a binary search tree cannot have duplicates, others the duplicate values will be on the right or can be on either side.  All are valid definitions.
*  __balanced__ vs __unbalanced__ does not mean that the left aznd right subtrees are exactly the same size (like a _perfect binary tree_).  It simply means that they're balanced enough to ensure `O(log n)` run times for `insert` and `find` operations.
*  A __complete binary tree__ is a binary tree where every level of the tree is fully filled, except for possibly the last level (filled left to right).  Examples of these include __min binary heaps__ and __max binary heaps__.
*  A __full binary tree__ is a binary tree in which every node has either zero or two children (no nodes have only one child)
*  A __perfect binary tree__ is a tree that is both full and complete.  All leaf nodes will be at the same level and this level has the maximum number of nodes.

__Traversing Trees__
You can traverse binary trees using a variety of methods (the most common being 'in-order' traversal):

*  __in-order traversal__ means to 'visit' the left branch, then the current node, and finally the right branch.  When performed on a binary search tree, it visits the nodes in ascending order (thus the name, in-order)
*  __pre-order traversal__ means to 'visit' the current node before its child nodes (thus the name, pre-order).  The root is always the first node visited.
*  __post-order traversal__ means to 'visit' the current node after its child nodes (thus the name, post-order).  The root is always the last node visited.

__Tries (prefix trees)__

A __trie__ (aka __prefix tree__) is a special kind of tree; a trie is like an n-ry tree in which characters are stored at each node and each path down the tree may represent a word.  

There are `*` nodes (aka __null nodes__) that are used to indicate complete words (e.g. `Many*` means many is a complete word or `Ma*` means there are lots of words that start with `Ma`).

A trie is commonly used to store the entire English language for quick prefix lookups.  A hash table can quickly look up whether a string is a valid word, but it cannot tell us if a string is a prefix of any valid words.

####<a id="trees">Graphs</a>

A tree is a type of __graph__, but not all graphs are trees.  A tree is a connected graph without cycles.  A graph is a collection of nodes with edges between (some of) them.  Graphs are used to model pairwise relations between objects. 

*  graphs can be either __directed__ (where edges are like a one way street) or __undirected__ (where edges are like a two way street).
*  graphs might consist of multiple isolated subgraphs.
*  if there is a path between every pair of vertices, it is a __connected graph__
*  graphs can (or not) have cycles.  an __acyclic graph__ is a graph without cycles

__Ways to store graphs__

You can store graphs as an __adjacency list__ (most common way) or as an __adjacency matrices__.

*  an __adjacency list__ is where every vertex (or node) stores a list of adjacent vertices in an undirected graph; an edge like (a, b) would be stored twice: once in a's adjacent vertices and once in b's adjacent vertices.  You can also store an adjacency list as an array (or a hash table) of lists like:
    -  0: 1
    -  1: 2
    -  2: 0, 3
    -  3: 2
    -  4: 6
    -  5: 4
    -  6: 5
*  an __adjacency matrix__ is an N*N boolean matrix (where N is the number of nodes), where a true value at `matrix[i][j]` indicates an edge from node i to node j (or you can use an integer matrix with 0's and 1's).  Usually searches on adjacency matrix are less efficient than an adjacency list since you have to search through all the nodes to identify a node's neighbors.

__Ways to search a Graph__

The two most common ways to search a graph are __depth-first search__ and __breadth-first search__.

*  In __depth-first search (DFS)__, we start at the root (or an arbitrarily selected node) and explore each branch completely before moving on to the next branch (thus the name depth-first) before we go wide.  This is usually a simpler approach.
*  In __breadth-first search (BFS)__, we start at the root (or an arbitrarily selected node) and explore each neighbor before going on to any of their children (thus the name breadth-first) before we go deep.
