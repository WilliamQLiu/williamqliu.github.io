---
layout: post
title: Technical Coding Interview Cheat Sheet
---

# {{ page.title }}

## Summary

I got into software development through a fairly non-traditional path (Peace Corps, Nonprofits, then Software Engineering). When I first started interviewing, I naively thought it would be behavioral questions (since that's the only questions that I've seen). Needless to say, I failed my first few interviews.

I'm writing this post as what I would tell my younger self on how to study for a technical code / skills assessment.

1. Coding for interviews is a much different skillset than what you do at work.
   You need to specifically practice for this skillset (sorry, just the reality at most companies; will save my thoughts on this for another post)
2. Pick one language (the simpler and less verbose the better; I use Python)
3. Read the first few chapters of Cracking the Coding Interview (Note: solutions are in Java)
   Optionally pick up a language specific book (e.g. Elements of Programming Interviews in Python) and get the gist of what questions/solutions look like
4. Learn those data structures and algorithms with [Neetcode.io](neetcode.io), a more targetted set of questions pulled from places like [Leetcode](leetcode.com) or other variations like [HackerRank](hackerrank.com).
5. Interview

### Question Types

The types of questions you'll see are:

* Arrays
* Hashes
* Two Pointers
* Sliding Window
* Stack
* Binary Search
* Linked List
* Trees
* Tries
* Heap / Priority Queue
* Backtracking
* Graphs
* Advanced Graphs
* 1-D Dynamic Programming
* 2-D Dynamic Programming
* Greedy
* Intervals
* Math & Geometry
* Bit Manipulation

## Arrays

### RAM

RAM / Memory is where data is stored.

Example Array: `[1, 3, 5]`
Example RAM: 8GB of RAM = 10^9 bytes
1 Byte = 8 bits
1 bit is a position and can be either `0` or `1`'s.
A single character can be 4 Bytes (i.e. 32 bits).

Arrays will always be a _contiguous_ allocation of RAM Addresses.

### Static Arrays

__Static Arrays__ have a __fixed size__ and generally not that useful (see Dynamic Arrays instead).
__Static Arrays__ are not available in some programming languages (e.g. Python or JavaScript),
which instead only offer Dynamic Arrays.


* Reading or Writing i-th element is fast `O(1)`.
* Inserting or Removing from the last element is fast `O(1)`.
* Inserting or Removing at the front or middle is `O(n)` worst case.

If you want to shift values (add or remove) in a static array (e.g. need to shift every value before or after),
it's an `O(n)` cost for worst case.

|Operation               | Big-O Time |
|----------------------- | ---------- |
|Read/Write i-th element | O(1)       |
|Insert/Remove End       | O(1)       |
|Insert Middle           | O(n)       |
|Remove Middle           | O(n)       |

### Dynamic Array

__Dynamic Arrays__ do not have a fixed size, but usually has a default size (with a length of 0).
Programming languages like Python use this as their default list.

* When you add elements, we __push__ elements to the end of the array `O(1)`
* When you remove elements, we __pop__ elements out from the end of the array `O(1)`
* When we run out of space, we allocate a brand new array with an increased capacity (and deallocate the old array) `O(n)`
* We know that it's fairly infrequent to double the space, so the __amortized complexity__ (fairly infrequent, average time) of `O(1)`

Note: In __Big O Notation__, we don't care about constant values in multiplying or adding to a variable (e.g. 20 * n, 2 + n).
We only care about constants raised to a power of a constant (e.g. n ^ 2).

|Operation               | Big-O Time |
|----------------------- | ---------- |
|Read/Write i-th element | O(1)       |
|Insert/Remove End       | O(1)       |
|Insert Middle           | O(n)       |
|Remove Middle           | O(n)       |

## Stack

__Stacks__ are basically a Dynamic Array. We push (add to the end) and pop (remove from the end).

* The last element added is the first element removed (__LIFO__).
* You can use a stack to reverse a sequence.

Example:
```
class Stack:
    def __init__(self):
        self.stack = []

    def push(self, n):
        self.stack.append(n)

    def pop(self):
        return self.stack.pop()
```

## Two Pointers

## Sliding Window

## Binary Search

## Linked List

## Trees

## Tries

## Heap / Priority Queue

## Backtracking

## Graphs

## Advanced Graphs

## 1-D Dynamic Programming

## 2-D Dynamic Programming

## Greedy

## Intervals

## Math & Geometry

## Bit Manipulation
