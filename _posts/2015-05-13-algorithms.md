---
layout: post
title: Algorithms
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
    - [What is an Algorithm](#whatisalgorithm)
    - [Is the algorithm correct?](#algorithmcorrect)
    - [Is the algorithm efficient?](#algorithmefficient)
*  [Algorithm Design](#algorithmdesign)
    - [Incremental](#incremental)
        + [mathmatical induction](#mathmaticalinduction)
        + [loop invariant](#loopinvariant)
    - [Divide and Conquer](#divideandconquer)
        + [recursion](#recursion)
*  [Sorting Problem](#problemsorting)
    - [Incremental: insertion sort](#insertionsort)
    - [Incremental: bubble sort](#bubblesort)
    - [Divide and Conquer: merge sort](#mergesort)
    - [Divide and Conquer: quick sort](#quicksort)
*  [Growth of Functions](#growthfunctions)
    - [Big O Notation](#bigonotation)
*  [Probabilistic Analysis](#probabilisticanalysis)
    - [The Hiring Problem](#hiringproblem)
    - [The Birthday Paradox](#birthdayparadox) 

TODO:

*  [Data Structures](#datastructures)
*  [Algorithm Techniques](#algorithmtechnique)
*  [Graph Theory](#graphtheory)
*  [Greedy Methods](#greedy)
*  [Dynamic Programming](#dynamicprogramming)
*  [NP complete](#npcomplete)
*  [Parallelism](#parallelism)


##<a id="summary">Summary</a>

####<a id="whatisalgorithm">What is an Algorithm?</a>

An __algorithm__ is a sequence of steps that takes an __input__ (i.e. some value(s)) and produces an __output__ (some value(s)).  For example, we might encounter a _sorting problem_ where we want to sort a sequence of numbers into nondecreasing order.

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

There are a variety of design approaches to algorithms.  Let's start with __incremental__ and __divide and conquer__, which are opposites of each other.

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

##<a id="problemsorting">Sorting Problems</a>

A basic computational problem is sorting a sequence of _n_ numbers (aka __keys__).  We apply the above approaches (insertion, divide and conquer) to different algorithms.

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

##<a id="growthfunctions">Growth of Functions</a>

We are interested in the __asymptotic__ efficiency of algorithms, which means we are estimating for very large inputs.  Usually an asymptotically efficient algorithm will be the best choice for everything but the smallest input cases.

####<a id="bigo">Big O Notation</a>
We use __Big O notation__ to give an estimated running time based on the input size (as it tends toward infinity)

##<a id="probabilisticanalysis">Probabilistic Analysis</a>

__Probabilistic analysis__ is the use of probability in the analysis of problems.  We can use this in analyzing the running time of an algorithm or we can use it to analyze other quantities, such as who to hire.

####<a id="hiringproblem">The Hiring Problem</a>

For this example, we want to hire an office assistant.  We interview candidates and determine if they are better than the current assistant (if so, replace the current assistant right then and there).  There is a cost to hiring and firing someone.  We want to find the expected cost after interviewing everyone (which is a fixed n candidates).

Say we have a list and rank them into an ordered list of best possible candidate using: `[rank1, rank2, rank3, rankn]`.  Saying that applicants come in a random order is equivalent to saying there is _n!__ permutations of the numbers 1 through n.  We call this __uniform random permutation__, which means that each of the possible n! permutations appears with equal probability.

We first assume (or make sure we randomly select) candidates for hire.  We can check probabilities and expectations using an __indicator random variable__.  For example, if we flip a coin, we count the number of times it actually comes up heads (saying using a __random number generator__) to what we expect.

####<a id="birthdayparadox">The Birthday Paradox</a>

How many people must there be in a room before there is a 50% chance that two of them are born on the same day of the year?  We have:

*  `k = number of people in the room`; we index the people in the room with integers (e.g. 1, 2, 3, ... k)
*  `n = 365 days`; we ignore leap years
