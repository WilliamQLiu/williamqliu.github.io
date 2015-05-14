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
    - [Incremental: Insertion Sort](#insertionsort)
    - [Divide and Conquer: Merge Sort](#mergesort)
    - [Divide and Conquer: Quick-sort](#mergesort)


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

The __divide and conquer__ approach is to break apart a large problem into several subproblems that are similar to the original problem but smaller in size.  We solve the subproblems __recursively__ (i.e. they call themselves) and then combine these solutions to create a solution to the original problem.

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

__Insertion sort__ is based on the incremental approach and is efficient at sorting a small number of elements in place.  For example, if we want to sort a hand of playing cards:

1. Start with an empty left hand and all cards face down on the table
2. We remove one card from the table and insert it into the correct position on the left hand
3. To find the correct position, we compare it with each of the cards already in the left hand (from right to left); this way left hand cards are always sorted

####<a id="quicksort">Divide and Conquer: merge sort</a>

__Merge sort__ uses the divide-and-conquer approach until the sorted sequence has a length of 1, then merges all the sorted sequences together.  Depending on the length of items, this approach usually has a faster _worst-case running time_ than insertion sort.  For example, if we want to sort a hand of playing cards:

1. __divide__ means splitting the n-element sequence into two subsequences of n/2 elements each
2. __conquer__ by sorting the two subsequences recursively using merge sort
3. __combine__ by merging the two sorted subsequences to produce the sorted answer

####<a id="bubblesort">bubble sort</a>

__Bubble sort__  (aka __sinking sort__, __ripple sort__) is a simple but inefficient sorting algorithm that repeatedly goes through the list to be sorted, compares each pair of adjacent items, and swaps them if they are in the wrong order.