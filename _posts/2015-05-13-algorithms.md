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
*  [Big O](#bigo)
    -  [Big O Example](#bigoexample)
    -  [Evaluating Runtimes](#bigotime)
    -  [Asymptotic Notation](#bigoasymptotic)
    -  [Amortized Notation](#bigoamortized)
    -  [Log N Runtimes](#lognruntimes)
    -  [Recursive Runtimes](#recursiveruntimes)
    -  [Space Complexity](#bigospace)
*  [Arrays and Strings](#arraysandstrings)
    -  [Hashing](#hashing)
    -  [Hash Tables](#hashtables)
    -  [Array Resizing](#arrayresize)
*  [Linked Lists](#linkedlists)
    -  [Create a linked list](#)
    -  [Delete a Node from a Singly Linked List](#)
    -  [The 'Runner' Technique](#)
    -  [Recursive Problems](#)
*  [Stacks and Queues](#stacksqueues)
    -  [Implement a Stack](#)
    -  [Implement a Queue](#)
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

There are different algorithms to solve the same problem and they are often drastically different in efficiency.  We normally measure efficiency by looking at __running time__, which is mainly affected by the __size of input__.  See __Big O__ for further details.

1. the __size of input__ normally means looking at the _number of items in the input_; for other problems like multiplying integers, we look at the _total number of bits_ used.
2. the __running time__ is the number of 'steps' (i.e. the number of times something is executed).

##<a id="bigo">Big O</a>

We use __Big O notation__ to give an estimated running time based on the input size (as it tends toward infinity).  Big O is basically a math formula that counts how many steps of computations get executed given a specified input size; the more computational steps the slower it is.  We characterize these functions according to their __rate of growth__ (aka __growth rate__), which is represented using the __O Notation__.

####<a id="bigoexample">Big O Example</a>

Say you want to get a hard drive of information to your friend across the country.  We can do an 'electronic transfer' through email or a 'physical transfer' by flying out there with the hard drive.  Each method has a different time complexity:

*  'Electronic Transfer' can be described as `O(s)` where `s` is the size of the file (for this example we say this is a linear function, but it can have a different __rate of growth__)
*  'Airplane Transfer' can be described as `O(1)` where `1` is a constant that simply means as the data gets larger, it doesn't affect our flight time.

If we compare these two scenarios, the 'Airplane Transfer' time is slower for small files, but at some point beats out the 'Electronic Transfer'.  This general idea is that the _running time of an algorithm is a function of the size of its inputs_.

####<a id="bigocommon">Common O Notations</a>

Common __O Notation__ runtimes are below (from the fastest first to slowest last):

*  `O(1)` - __constant__; e.g. check if a number is even or odd (uses a constant-size lookup or hash table)
*  `O(log N)` - __logarithmic__; e.g. find an item in a sorted array with a binary search (because we split in half)
*  `O(N)` - __linear__; e.g. find an item in an unsorted list
*  `O(N log N)` - __loglinear__; e.g. heapsort, quicksort (best and avg case), merge sort
*  `O(N^2)` - __quadratic__; e.g. selection sort, insertion sort, worst case for bubble sort, quicksort
*  `O(2^N)` - __exponential__; e.g. finding exact solution to traveling salesman problem using dynamic programming
*  `O(N!)` - __factorial__; e.g. solving traveling salesman problem via brute-force search

A good cheatsheet is [here](http://bigocheatsheet.com/ "Big O Cheatsheet")

####<a id="bigotime">Evaluating Runtimes</a>

When evaluating time, we can look at the __O Notation__ a few different ways.

*  __big omega__ looks at the __worst-case running time__; this is the norm
*  __big theta__ looks at the __average-case running time__; this is usually used for _probabilistic analysis_.
*  __big O (aka O)__ looks at the __best-case running time__.

####<a id="bigoasymptotic">Asymptotic Notation</a>

What really happens is that we're interested in the __asymptotic notation__ runtime, which is how the algorithm runtime scales for very large inputs and not in the minute details or small inputs.  This means we:

*  Drop the Constants (e.g. `O(2N)` is actually `O(N)`)
*  Drop the Non-Dominant Terms (e.g. `O(N^2 + N^2)` is actually `O(N^2)`)
    -  E.g. `O(N + log N)` becomes `O(N)`
    -  E.g. `O(N^2 + N)` becomes `O(N^2)`
*  Keep the terms that grow bigger when `N` approaches `infinity`

__Add or Multiple__

If we do code chunk A completely then start code chunk B, the notation should look like `O(A + B)`.  If we do code chunk A and code chunk B is in code A (e.g. for-loop in a for-loop), the notation should look like `O(A * B)`.

####<a id="bigoamortized">Amortized Time</a>

__Amortized time__ is like doing an operation say a million times.  You don't care about the worse-case or best-case scenario every once in a while.  We only care about the time taken in total to do the million operations.

####<a id="lognruntimes">Log N Runtimes</a>

`O(log N)` is a common runtime because of the __elements being halved__ scenario.  For example, in a binary search for `x` in an N-element sorted array, the options are:

1. if `x==middle` then return middle
2. if `x<middle` then search left
3. if `x>middle` then search right

The runtime for this looks like this (say given an N-element array of 16):

1. N=16 # divide by 2
2. N=8  # divide by 2
3. N=4  # divide by 2
4. N=2  # divide by 2
5. N=1  # divide by 2

We could reverse this to say how many times can we multiply by 2 until we get N?

1. N=1  # multiply by 2
2. N=2  # multiply by 2
3. N=4  # multiply by 2
4. N=8  # multiply by 2
5. N=16 # multiply by 2

We then get `2^k=N`, which is what `log` expresses.  For example:

*  `2^4=16`, which is `logBase(16,2)=4`  where `logBase(value, base)`
*  `logBase(N,2)=k`, which is `2^k=N`

####<a id="bigorecursive">Recursive Runtimes</a>

When you have a recursive functions that makes multiple calls, the runtimes will often (not always) look like `O(branches^depth)` where 'branches' is the number of times each recursive call branches.

####<a id="bigospace">Space Complexity</a>

Besides time, other considerations in real life (that we won't consider for now) are things like space (RAM, hard drive), bandwidth speed, caches, and parallelism (single, multiple cores).

The general rule is if we need to create an array of size `n`, we would require `O(n)` space.

##<a id="arraysandstrings">Arrays and Strings</a>

####<a id="hashing">Hashing</a>

We do __hashing__ because of its speed.  If we know information about the structure of our data (e.g. if a list is ordered or not), we can use this knowledge to do efficient searches (e.g. in `O(log N)` runtime using a _binary search_ instead of `O(N)`).

If we know where every item should be, then our search can do a single comparison to find the item.  This concept is called __hashing__ and it has a really efficient runtime.  The idea is that when we input a key (e.g. Will) through a __hash function__, this returns a map to where the data is stored (aka the __hash values__, __hash codes__, __hash sums__, __hashes__).

####<a id="hashtable">Hash Table</a>

A __hash table__ (aka __hash map__) is a collection of items that are stored in a way where we can find the items very quickly.  Each position in the hash table is called a __slot__ (sometimes the entire slot is refered to as a __bucket__) and can hold an item.

####<a id="hashfunction">Hash Function</a>

A __hash function__ is the function that distributes key/value pairs across an array of slots.  A sample function would look like `index = f(key, array_size` and `hash = hashfunc(key)`.  The goal of the hash function is to:

*  Be deterministic - i.e. equal keys produce the same hash value all the time
*  Be efficient - Computing the hash should be quick
*  Be uniform - distribute the keys uniformly so they do not cluster

A __perfect hash function__ is the case where every item is mapped to a unique slot, which then runs in `O(1)` time.  We don't need a perfect hash function in order for the hash to be efficient.

####<a id="hashexample">Hash Example</a>

Say we have a small __hash table__ represented by a list of 11 empty slots (`m=11`) that have Python value of `None`.  Note that the number of empty slots should ideally be __prime__ (so we can have an easier way of resolving _hash collisions_, more on that later).

    slots       0    1    2    3    4    5    6    7    8    9    10
    values      None None None None None None None None None None None

Say we need to put in a set of integer items, like this: `54, 26, 93, 73`

We can create a __hash function__ that takes an item in the collection and returns an integer that tells what slot (between `0` and `m-1`) to place the item.  A common hash function is the __remainder method__, where we take an item and divide by the table size, returning the remainder as the hash value.  

    54 % 11 = remainder of 10
    26 % 11 = remainder of 4
    93 % 11 = remainder of 5
    73 % 11 = remainder of 7

After we calculate the hash values, we enter them into the hash table.  The __load factor__ is the `numberofitems / tablesize` (e.g. 4 items/ 10 buckets).  Conceptually, if the _load factor_ is small, there are potentially less collisions; if the _load factor_ is large, there are potentially more collisions (which also means _collision resolution_ is more difficult). Usually a load factor of `.8` or higher is where linear probing's performance really degrades and you should definitely do `chaining`.  Here we have our updated hash table:

    slots       0    1    2    3    4    5    6    7    8    9    10
    values      None None None None 26   93   None 73   None None 54

####<a id="hashcollision">Hash Collision</a>

So what happens in our example if we add a new item of `71`?  We would get a remainder of `5`, which would create a __hash collision__ with `93`; this means at least two values are trying to fit into the same bucket (in our example, the values of `93` and `71`).  We have two goals:

1. __minimize hash collisions__ - what can we do to prevent hash collisions?
2. __hash resolution__ - despite our best efforts to prevent hash collision, we'll probably run into them; what can we do once a hash collision happens?

####<a id="hashminimizecollision">Minimizing Hash Collision</a>

As we look at different ways to minimize hash collision, we should keep in mind that you want a function to __uniformly distribute__ hash values.  These can be tested using statistical tests (e.g. __Pearson's chi-squared test__ for _discrete uniform distributions_)

####<a id="hashfolding">Minimizing Hash Collision with the Folding Method</a>

One way of trying to minimize hash collision is with the __folding method__.

1. The idea is that we divide our items into equal sized pieces (with the last piece probably not the same size).  e.g. if we have a phone number of `436-555-4601`, we would take the numbers and divide them into groups of 2, like `43, 65, 55, 46, 01`.
2. We then add all the pieces together to get a resulting hash value.  e.g. `43+65+55+46+1 = 210`
3. With the resulting hash value (e.g. `210`), we do the modulus to get a remainder of `1` (`210%11=1`) to place the value `210` into position `1`.
4. Optionally: Some _folding methods_ reverse every other piece before the addition.  e.g. `43+65+55+46+01` turns into `43+56+55+64+01=219`, which `219%11=10`

####<a id="hashmidsquare">Minimizing Hash Collision with the Mid-Square Method</a>

Another way of trying to minimize hash collision is with the __mid-square method__.

1. The idea is that we first square the item, then extract some porition of the resulting digits.  e.g. if the item is `44`, we calculate `44^2=1936`.
2. The next step is extracting the middle two digits (e.g. `93` from `1936`) and taking the modulus to get the remainder (`93%11=5`)

####<a id="hashchars">Minimizing Hash Collision when Hashing Characters</a>

We can create hash functions for character-based items like strings.  For example, we have: `99+97+116=312`, then `312%11=4`.

    ord('c')  # 99
    ord('a')  # 97
    ord('t')  # 116

If we do a hash function that just simply adds up all the characters and gets the modulus, we end up with _hash collision_ for anagrams (e.g. `dad`).  We can fix this by adding a weighted position to the character (e.g. multiply by 1 for first char, multiply by 2 for second char, etc).  For example:

    def hash(astring, tablesize):
        sum = 0
        for position in range(len(astring)):
            sum = sum * position + ord(astring[position])
        return sum%tablesize

###<a id="hashcollisionresolution">Collision Resolution</a>

__Collision resolution__ is the method for resolving _hash collision_, which means what do we do when two or more items are hashed to the same slot in the _hash table_.  There are many ways of attempting to address this including __open addressing__ and __separate chaining__. 

####<a id="hashopenaddress">Resolving Hash Collision with Open Addressing</a>

__Open Addressing__ (aka __closed hashing__) is a method to resolve _hash collisions_ by trying to find the next open slot in the _hash table_ through some __probe sequence__, which systematically visits each slot until an open slot is found.  The name comes from the fact that the location ('address') of the item is not determined by its 'hash value'.  The downside of _open addressing_ is that the number of stored entries cannot exceed the number of slots.

There are many probe sequences, which has different takes on trying to minimize clustering of hash values.  These probing sequences include:

*  __linear probing__ - where the interval between probes is fixed (e.g. 1, every other)
*  __quadratic probing__ - where the interval between probes is increased by adding the successive outputs of a quadratic polynomial to the starting value given by the original hash
*  __double hashing__ - where the interval between probes is computed by another hash function

####<a id="hashlinearprobe">Resolving Hash Collision with Open Addressing and Linear Probing</a>

When we visit each bucket one at a time, we are using a technique called __linear probing__.  With the given example above, if we added `71`, we would have a _hash collion_ since `93` is already in position `5` (`71%11=5` and `93%11=5`).  We call this __rehashing__ when we try to make `71` into a new hash.

    slots       0    1    2    3    4    5    6    7    8    9    10
    values      None None None None 26   93   None 73   None None 54

####<a id="hashquadraticprobe">Resolving Hash Collision with Open Addressing and Quadratic Probing</a>

Instead of making the skip a constant value (e.g. 1, 2), we can use a rehash function that increments the hash value by 1, 3, 5, 7, etc.  This means that if the first hash value is `h`, the successive hash values are `h+1`, `h+4`, `h+9`, `h+16`, `h+25` etc.  This 'skip' value is successive perfect squares.

####<a id="hashquadraticprobe">Resolving Hash Collision with Separate Chaining</a>

__Separate chaining__ (aka __chaining__) allows many items to exist at the same location in the hash table.  When a hash collision happens, the item does not do probing and instead is placed in the proper slot.

When we search for an item, we use the hash function to generate the slot where it should be.  Each slot has a collection so we use a searching technique to decide if the item is present.  The advantage is that on average, there are likely to be very few items in each slot so the search should be more efficient.

##<a id="linkedlists">Linked Lists</a>

####<a id="singelinkedlist">Singly Linked List</a>


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

__Recursion__ is a method where the solution to a problem depends on solutions to smaller instances of the same problem.  You can visually see this as a __recursion tree__, which is a tree diagram of recursive calls.  Recursion has to obey three laws:

1. A recursive algorithm must have a __base case__.
2. A recursive algorithm must changes its state and move toward the base case.
3. A recursive algorithm must call itself, recursively.

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
    print word
    print removeDups(word)  # abcd

Other examples of recursive problems include:

*  The Tower of Hanoi
*  Exploring a Maze

##<a id="comparisonsortalgorithms">Sorting Algorithms with Comparison Sorts</a>

A basic computational problem is the __sorting problem__, where you sort a sequence of _n_ numbers (aka __keys__).  We apply the above general approaches (_insertion_, _divide and conquer_) using different types of algorithms.  The following algorithms (_insertion sort_, _bubble sort_, _selection sort_, _merge sort_, _heapsort_, and _quicksort_) are all __comparison sorts__ (i.e. they determine the order of an input array by comparing elements).

####<a id="insertionsort">Incremental: insertion sort</a>

__Insertion sort__ is a simple sorting algorithm based on the incremental approach and is efficient at sorting a small number of elements in place.  The idea is to always maintain a sorted sublist in the lower portions of the list. Each new item is then 'inserted' back into the previous sublist so that the sorted sublist is one item larger.  For example, if we want to sort a hand of playing cards:

1. Start with an empty left hand and all cards face down on the table
2. We remove one card from the table and insert it into the correct position on the left hand
3. To find the correct position, we compare it with each of the cards already in the left hand (from right to left); this way left hand cards are always sorted

Example Code: 

    """ Insertion Sort """
    
    def insertionSort(mylist):
        for index in range(1, len(mylist)):
            print "Index is ", index  # 1, 2, 3, 4, 5, 6, 7, 8; this is the outer loop
    
            # setup first case (only one item)
            currentvalue = mylist[index]
            position = index
    
            # this is the inner loop, loops through the sorted list backwards and compares values
            while position > 0 and mylist[position-1] > currentvalue:
                mylist[position] = mylist[position-1]
                position = position - 1
    
            mylist[position] = currentvalue  # found spot in inner sorted loop to place item
    
    if __name__ == '__main__':
        mylist = [54,26,93,17,77,31,44,55,20]
        print "Original: ", mylist
        insertionSort(mylist)
        print "Insertion Sorted: ", mylist


####<a id="bubblesort">Incremental: bubble sort</a>

__Bubble sort__  (aka __sinking sort__, __ripple sort__) is a simple but inefficient sorting algorithm that repeatedly goes through the list to be sorted, compares each pair of adjacent items, and swaps them if they are in the wrong order.  For example, say we were sorting scrabble tiles into alphabetical order.

1. Place letters on tile holder and look at the first block.
2. Look at the block to the right of it.
3. If the block to the right should come before the block on the left, swap them.
4. Compare the next block in line with the first and repeat step 3
5. Begin step 1 again with the second block

The name bubble sort is because elements tend to move up into the correct order like bubbles rising to the surface and you see a rippling effect for the ones that are not in the correct order.  After each pass of the bubble sort, one item is definitely sorted; a total of `n-1` passes to sort `n` items.  Big Oh Runtime is `O(n^2)`.

Example Code

    # Bubble Sort
    def bubbleSort(mylist):
        for passnum in range(len(mylist)-1, 0, -1):
            #print passnum  # backwords (8,7,6,..2,1) b/c other items are already sorted
            for i in range(passnum):
                if mylist > mylist[i+1]:  # compare neighbors
                    mylist, mylist[i+1] = mylist[i+1], mylist  # swap
    
    if __name__ == '__main__':
        mylist = [54,26,93,17,77,31,44,55,20]
        print "Original: ", mylist
        bubbleSort(mylist)
        print "Bubble Sorted: ", mylist

####<a id="selectionsort">Incremental: selection sort</a>

__Selection sort__  improves on _bubble sort_ by making only one exchange for every pass through the list.  The selection sort finds the largest value as it makes its pass and after completing the pass, places it in the correct location/order.  What happens is that there is a 'swap' (where we put the largest value into the largest index; the item that was previously in the largest index is swapped over to where the previous largest value was).

Similar to bubble sort, after the initial pass, the largest item appears in place.  The final item is in place after `n-1` passes to sort `n` items.  This is slightly faster than bubble sort since we don't have to do as many exchanges.  Big Oh Runtime is still `O(n^2)`.

    """ Selection Sort """
    
    def selectionSort(mylist):
        for fillslot in range(len(mylist)-1, 0, -1):
            #print fillslot  # backwords (8,7,6,..2,1) b/c other items are already sorted
            positionOfMax = 0
            for i in range(1, fillslot+1):
                if mylist[i] > mylist[positionOfMax]:  # is value greater than value at max
                    positionOfMax = i
    
            # to move the largest value to the largest index, we 'swap' the item
            # currently in the largest index position
            mylist[fillslot], mylist[positionOfMax] = mylist[positionOfMax], mylist[fillslot]
    
    
    if __name__ == '__main__':
        mylist = [54,26,93,17,77,31,44,55,20]
        print "Original: ", mylist
        selectionSort(mylist)
        print "Selection Sorted: ", mylist

####<a id="mergesort">Divide and Conquer: merge sort</a>

__Merge sort__ is a recursive algorithm that uses the divide-and-conquer approach to keep splitting a list in half until there are individual items.  We then go in reverse (instead of splitting), we combine the items back together using a __merge__.

For example, if we want to sort a hand of playing cards:

1. __divide__ means splitting the n-element sequence into two subsequences of n/2 elements each
2. __conquer__ by sorting the two subsequences recursively using merge sort
3. __combine__ by merging the two sorted subsequences to produce the sorted answer

Merge sort is good for data that is too big to fit into memory at once because the pattern of storage is regular.  It is especially good for data stored as linked lists.

Example Code:

    """ Merge Sort """
    
    def mergeSort(mylist):
        print "Splitting", mylist
    
        if len(mylist) > 1:
            mid = len(mylist) // 2
            lefthalf = mylist[:mid]
            righthalf = mylist[mid:]
        
            mergeSort(lefthalf)
            mergeSort(righthalf)
        
            # below code merges the two smaller sorted lists to larger sorted list
            i = 0  # left half index
            j = 0  # right half index
            k = 0  # main / large sorted list
        
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
                mylist[k] = lefthalf[i]
                i += 1
                k += 1
        
            # insert remaining values from righthalf
            while j < len(righthalf):
                mylist[k] = righthalf[j]
                j += 1
                k += 1
        print "Merging", mylist
    
    if __name__ == '__main__':
        mylist = [54,26,93,17,77,31,44,55,20]
        print "Original: ", mylist
        mergeSort(mylist)
        print "Merge Sorted: ", mylist


####<a id="quicksort">Divide and Conquer: quick sort</a>

__Quick sort__ is an efficient algorithm that does a sort 'in place' by splitting the array into two smaller arrays, one with low elements and one with high elements based off a 'pivot' element.  It's similar to _merge sort_ in that it does the divide and conquer approach.  The advantage is that you do not need as much storage over _merge sort_, but the performance could possibly be diminished (depending on if the __pivot value__ selected is near the middle).

1. Pick an element from the array; this element is called a __pivot__
2. We now want to do a __partition__ operation; this means we want to reorder the array so that all elements with values less than the pivot are on one side while all elements with values greater than the pivot are on the other side (equal values can go either way).  After this paritioning, the pivot element is in its final position.
3. Partitions begin by locating two position markers (e.g. _leftmark_ and _rightmark_) at the beginning and end of the remaining items in the list.  The goal of the partition process is to move items that are on the wrong side with respect to the pivot value while also converging on the split point.
4. We increment __leftmark__ until we locate a value that is greater than the pivot value.  We then decrement __rightmark__ until we locate a value less than the pivot value.  When this happens, we exchange the two items and repeate the process again.

Example Code:

    #quicksort


####<a id="heapsort">Divide and Conquer: heap sort</a>

__Heap sort__ takes the best properties of merge sort (the run time) and the efficency of insertion sort's ability to sort in place (i.e. only a constant number of elements are stored outside the input array at any time).  What makes heapsort unique is that it uses a data structure called a __heap__ to help manage information (instead of a __linear-time search__); heapsort divides its input into a sorted and an unsorted region and it iteratively shrinks the unsorted region by extracting the largest element and moving that to the sorted region.  This heapsort is really efficient at managing __priority queues__.

Note: In programming languages like Java or Lisp, heap refers to 'garbage-collected storage'; this is not what we're talking about here.

Given an array _A_ that represents a heap, we can look at the two attributes (_length_, _heap-size_) and determine what part is valid (i.e. this is the correctly sorted region) and what is still the heap (i.e. unsorted):

*  `A.length` gives us the number of elements in the array
*  `A.heap-size` gives us how many elements in the heap are stored within array _A_.
*  The heap would then be calculated as `A[1 ... A.heap-size]` where `0 <= A.heap-size <= A.length`

####<<a id="binaryheap">(Binary) Heap</a>

A __(binary) heap__ data structure is an array object that we can view as a binary tree.  Think of this algorithm as two parts:

1. We have some data (e.g. a list of `[6, 5, 3, 1, 8, 7, 2, 4]`) that we use to create the _heap_, a data structure that looks like a binary tree.  As we're building this binary tree, the heap swaps elements depending on the type (e.g. min or max) of the binary heap (e.g. sorting smallest to largest, larger nodes don't stay below smaller node parents and end up swapping; `8` can't be below `5` on the heap).  Once the binary tree is built, we have a tree where each array index represents a node and also has the index of the node's parent, left child branch, or right child branch.
2. We then create a _sorted array_ by repeatedly removing the largest element from the root of the heap and inserting it into the array.  The heap is updated after each removal to maintain the heap.  The heap incrementally gets smaller until all objects have been removed from the heap, resulting in only a sorted array left.

####<<a id="priorityqueue">Priority Queue</a>

As mentioned earlier, __heap sort__ is great for creating __priority queues__, which is a data structure for maintaining a set _S_ of elements, each with an associated value called a __key__.  There's __max-priority queue__ (e.g. used to schedule jobs on a server and set their relative priorities) and a __min-priority queue__ (e.g. used for _discrete event-driven simulation_ models like determining how many patients can be served from changing 8 hours to 9 hours of operation when avg surguery takes say 4 hours).

##<a id="decisiontree">Sorting Algorithms with Linear Time (Decision Tree Models)</a>

Previously mentioned algorithms are __comparison sorts__, which determines the sort order based only on comparisons between the input elements.  If we make the assumption that all the input elements are distinct, we can sort by __linear time__.  This means we can do comparison sorts in terms of __decision trees__, which is a full binary tree that represents the comparisons between elements in a sorting algorithm (say elements `i` : `j`).  

####<a id="countingsort">Counting Sort</a>

Placeholder for smart stuff.

####<a id="radixsort">Radix Sort</a>

Placeholder for smart stuff.

####<a id="bucketsort">Bucket Sort</a>

Placeholder for smart stuff.

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
*  Each tree has a __root node__
*  The root node has zero or more child nodes
*  Each child node has zero or more child nodes, and so on.

The tree cannot contain cycles.  The nodes may or may not be in a particular order, they could have any data type as values, and they may or may not have links back to their parent nodes.

__Real life examples of trees__

*  A web site
    -  Starts at `<html>`
    -  Next level has `<head>` node and `<body>` node
    -  `<head>` splits into `<meta>` and `<title>`; `<body>` splits into `<h1>` and `<h2>`
*  File system on a computer (e.g. UNIX)
    -  Starts at `/`
    -  Next level has `dev/`, `etc/`, `usr/`, `var/`, etc.

__Pieces of Trees__

*  __Node__ has a name (the __key__) and can also contain additional information (the __payload__).  The highest node is the __root node__, which has no parent.
*  __Edge__ - an edge connects two nodes to show that there is a relationship between them.  Every node except the root node has one incoming edge from another node.  Each node can have several outgoing edges.
*  __Path__ - a path is an ordered list of nodes that are connected by edges.  E.g. `C:` -> `Users\` -> `WLiu` -> `My Documents`
*  __Parent__ - node A is the parent node of B if A has outgoing edges that connect to B.
*  __Children__ - node B is a child node of node A if B has incoming edges from A.
*  __Siblings__ - node C and B are siblings if they share the same parent node A.
*  __Subtree__ - A subtree is a set of nodes and edges with parent A and all the descendants of that parent.
*  __Leaf Node__ - A leaf node is a node that has no children.
*  __Level__ - the level of a node n is the number of edges on the path from the root node to n.  The level of the root node is zero.
*  __Height__ - the height of a tree is the maximum level of any node in the tree.

__How to appraoch trees__

We can think of trees two different ways, as a large set of rules or as a smaller recursive set of rules:

1. A tree consists of a set of nodes and a set of edges that connect pairs of nodes.  Trees have the following properties:
    -  One node of the tree is designed as the root node
    -  Every node _n_, except the _root node_, is connected by an edge from exactly one other node _p_, where _p_ is the parent of _n_.
2. Think recurisvely; a tree is either empty or consists of a root and zero or more subtrees, each of which is also a tree.

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

*  __pre-order traversal__ means to visit the current node before its child nodes (thus the name, pre-order).  The root is always the first node visited.
*  __in-order traversal__ means to visit the left branch, then the current node, and finally the right branch.  When performed on a binary search tree, it visits the nodes in ascending order (thus the name, in-order)
*  __post-order traversal__ means to visit the current node after its child nodes (thus the name, post-order).  The root is always the last node visited.

__Tries (prefix trees)__

A __trie__ (aka __prefix tree__) is a special kind of tree; a trie is like an n-ry tree in which characters are stored at each node and each path down the tree may represent a word.  

There are `*` nodes (aka __null nodes__) that are used to indicate complete words (e.g. `Many*` means many is a complete word or `Ma*` means there are lots of words that start with `Ma`).

A trie is commonly used to store the entire English language for quick prefix lookups.  A hash table can quickly look up whether a string is a valid word, but it cannot tell us if a string is a prefix of any valid words.

####<a id="graphs">Graphs</a>

A tree is a type of __graph__, but not all graphs are trees.  A tree is a connected graph without cycles.  A graph is a collection of nodes with edges between (some of) them.  Graphs are used to model pairwise relations between objects.

*  graphs can be either __directed__ (where edges are like a one way street) or __undirected__ (where edges are like a two way street).
*  graphs might consist of multiple isolated subgraphs.
*  if there is a path between every pair of vertices, it is a __connected graph__
*  graphs can (or not) have cycles.  an __acyclic graph__ is a graph without cycles

####<a id="rlexamplegraphs">Real Life examples of Graphs</a>

Computers can use graphs to find the shortest, quickest, or easiest path from one place to another.  Some real life scenarios include:

*  systems of roads
*  airline flights from city to city
*  how the internet is connected
*  sequence of classes you must take to complete your college major

####<a id="classicexamplegraphs">Classic examples of Graphs</a>

*  The 'Word Ladder Problem' puzzle by Lewis Carroll where you change a word into another word, can only change one letter at a time and each step you transform the word must still be another word (e.g. FOOL > POOL > POLL >... > SAGE)
*  The 'Knights Tour Problem' is a puzzle on the chess board with a single chess piece (the knight).  The object of the puzzle is to find a sequence of moves that allow the knight to visit every square on the board exactly once.  One sequence is a 'tour'.
    -  Each square on the chessboard is represented as a node in the graph.
    -  Each legal move by the knight is represented as an edge

####<a id="vocabdefinegraphs">Vocabulary and Definitions for Graphs</a>

A graph can be represented by `G=(V,E)` for the graph `G`, `V` is a set of vertices and `E` is a set of edges.  Each edge is a tuple `(v, w)` where `w, v` makes up `V`.  Optionally, we can add a third component to the edge tuple to represent a weight.

*  __vertex__ (aka a __node__) has a name (the __key__) with additional information (the __payload__).
*  __edge__ (aka an __arc__) connects two vertices to show their relationship.  Edges can be __one-way__ (creating a __directed graph__, aka __digraph__) or __two-way__ (creating an __undirected graph__).
    -  When two 'vertices' are connected by an edge, they are __adjacent__.
    -  Example of a 'directed graph' is if Person A knows Person B, but Person B does not necessarily know Person A
    -  Example of an 'undirected graph' is if Person A shakes hands with Person B, Person B has also shaken hands with Person A
*  __weight__ is the cost to go from one vertex to another.
    -  Example is a graph of roads that connect one city to another; the weight on the edge might represent the distance between the two cities.
*  __Path__ in a graph is a sequence of vertices that are connected by edges.
*  __Cycle__ in a directed graph is a path that starts and ends at the same vertex.
    -  A graph with no cycles is an __acyclic graph__.
    -  A 'directed graph' with no cycles is called a __directed acyclic graph__ (aka a __DAG__).

####<a id="graphabstractdatatype">Graph Abstract Data Type (ADT)</a>

This is how to setup a graph abstract data type (ADT) that we can build off of:

*  `Graph()` creates a new, empty graph
*  `addVertex(vert)` adds an instance of `Vertex` to the graph
*  `addEdge(fromVert, toVert)` adds a new, directed edge to the graph that connects two vertices
*  `addEdge(fromVert, toVert), weight)` adds a new, weighted, directed edge to the graph that connects two vertices
*  `getVertex(vertKey)` finds the vertex in the graph named `vertKey`
*  `getVertices()` returns a list of all vertices in the graph
*  `in` returns `True` for the statement: `vertex in graph` if the given vertex is in the graph, `False` otherwise


####<a id="storegraphs">Ways to store a graph (adjacency list and matrix)</a>

You can store graphs as an __adjacency list__ (most common way) or as an __adjacency matrices__.  When two verticies are connected by an edge, they are __adjacent__, thus the name of list and matrix.  There are advantages and disadvantages to both.

*  an __adjacency matrix__ is a two-dimensional matrix, more precisely a N*N boolean matrix (where N is the number of nodes).  A value at `matrix[v][w]` means row `v` and column `w`, which indicates an edge from node v to node w.
    -  Advantages: The adjacency matrix is simple, especially for small graphs where you can see which nodes are connected to other nodes.
    -  Disadvantages: Usually searches on an adjacency matrix are less efficient than an adjacency list since you have to search through all the nodes to identify a node's neighbors.  Since most of the cells are usually empty (i.e. a __sparse matrix__), this is not an efficient way to store data.  It is rare to see a real world problem where most vertexes connect to most other vertexes.

__Example adjacency matrix__

        v0  v1  v2  v3  v4  v5
    v0       5               2
    v1           4
    v2               9
    v3                   7   3
    v4   1
    v5           1       8

*  an __adjacency list__ implementation involves keeping a master list of all the vertices in the Graph object and then each vertex object in the graph maintains a list of the other vertices that it is connected to.  We can implement the `Vertex` class as a dictionary or a list (below sample is as a dict where the keys are the vertices and the values are the weights)
    -  Advtanges:  More space-efficient to implement a sparesely connected graph.

__Example adjacency list__

    v0: id=v0, adj={v1:5, v5:2}
    v1: id=v1, adj={v2:4}
    v2: id=v2, adj={v3:9}
    v3: id=v3, adj={v4:7, v5:3}
    v4: id=v4, adj={v0:1}
    v5: id=v5, adj={v2:1, v4:8}

####<a id="adjacencylistimplement">Adjacency List Implementation</a>

To implement an 'adjacency list', we need to implement:

*  `Graph`, which is a master list of the vertices; this contains a dictionary that maps vertex names to vertex objects.
*  `Vertex`, which represents each vertex in the graph.  Each vertex uses a dictionary to keep track of the vertices to which it is connected and the weight of each edge.


####<a id="searchgraphs">Ways to search a graph</a>

After constructing a graph.  The two most common ways to search a graph are __depth-first search__ and __breadth-first search__.

*  In __depth-first search (DFS)__, we start at the root (or an arbitrarily selected node) and explore each branch completely before moving on to the next branch (thus the name depth-first) before we go wide.  This is usually a simpler approach.

*  In __breadth-first search (BFS)__, we start at the root (or an arbitrarily selected node) and explore each edge/neighbor before going on to any of their children (thus the name breadth-first) before we go deep.  To keep track of which vertices have been visited, we color them to:
    -  `white` - all vertices are initialized as white when constructed; this means the vertex is undiscovered (i.e. not visited yet)
    -  `gray` - when a vertex is initially discovered it is colored gray.  When a vertex is colored gray, there may be some white vertices adjacent to it (indicating that there are still additional vertices to explore)
    -  `black` - when a vertex is completely explored, it is colored black.  Once a vertex is black, there are no white vertices adjacent to it.

The heart of a __BFS__ is a `Queue`, which decides which vertex to explore next.  


