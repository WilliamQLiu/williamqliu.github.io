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
* Stack
* Hashes
* Two Pointers
* Sliding Window
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
|Insert/Remove Beginning | O(n)       |
|Insert/Remove Middle    | O(n)       |
|Insert/Remove End       | O(1)       |


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
|Insert/Remove Beginning | O(n)       |
|Insert/Remove Middle    | O(n)       |
|Insert/Remove End       | O(1)       |

### Stacks

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
## Linked List

### Singly Linked List

A __Singly Linked List__ is a node that points to other node(s).

* A Node (aka List Node) has a value and a pointer to the next Node (aka List Node).
* Linked Lists are not stored as a contiguous memory (different than Arrays); doesn't need a block of memory
* When the last node is null, it means it's the end

E.g. ListNode1 (value, pointer to ListNode2), ListNode2 (value, pointer to ListNode3)

```
class ListNode:
    def __init__(self, value):
        self.value = value
        self.next = None

# Singly Linked List
class LinkedList:
    def __init__(self):
        # init the list with a 'dummy' node
        self.head = ListNode(-1)
        self.tail = self.head

    def insertEnd(self, value):
        self.tail.next = ListNode(value)
        self.tail = self.tail.next

    def remove(self, index):
        i = 0
        current = self.head
        while i < index and current:
            i += 1
            current = current.next

        # Remove the node ahead of current, need to set .next.next to get next (after removed)
        if current:
            current.next = current.next.next

    def print(self):
        current = self.head.next
        while current:
            print(current.value, ' -> ')
            current = current.next
        print()
```

|Operation               | Big-O Time |
|----------------------- | ---------- |
|Read/Write i-th element | O(n)       |
|Insert/Remove Beginning | O(1)       |
|Insert/Remove Middle    | O(n)       |
|Insert/Remove End       | O(n)       |

### Doubly Linked List

A __Doubly Linked List__ is similar to a Singly Linked List, except each node has two pointers (prev, next)

* The first node's previous is null
* The last node's next is null
* Stacks can be implemented as a Linked List, but don't because a disadvantage of a Linked List is that we can't access i-th element in `O(1)`, instead is `O(n)`
* One advantage is inserting or removing from the beginning or middle of a Linked List is `O(1)` instead of an Array's `O(n)`

Example:
ListNode1 (prev = null, value, next = pointer to ListNode2),
ListNode2 (prev = pointer to ListNode1, value, next = pointer to ListNode3)
ListNode3 (prev = pointer to ListNode2, value, next = null)

|Operation               | Big-O Time |
|----------------------- | ---------- |
|Read/Write i-th element | O(n)       |
|Insert/Remove Beginning | O(1)       |
|Insert/Remove Middle    | O(n)       |
|Insert/Remove End       | O(1)       |

```
class ListNode:
    def __init__(self, value):
        self.value = value
        self.next = None
        self.prev = None

# Implementation for Doubly Linked List
class LinkedList:
    def __init__(self):
        # Init the list with 'dummy' head and tail nodes
        # which makes edge cases for insert and remove easier
        self.head = ListNode(-1)
        self.tail = ListNode(-1)
        self.head.next = self.tail
        self.tail.prev = self.head

    def insertFront(self, value):
        newNode = ListNode(value)
        newNode.prev = self.head
        newNode.next = self.head.next

        self.head.next.prev = newNode
        self.head.next = newNode

    def insertEnd(self, value):
        newNode = ListNode(value)
        newNode.next = self.tail
        newNode.prev = self.tail.prev

        self.tail.prev.next = newNode
        self.tail.prev = newNode

    # Remove first node after dummy head (assume it exists)
    def removeFront(self):
        self.head.next.next.prev = self.head
        self.head.next = self.head.next.next

    # Remove last node before dummy tail (assume it exists)
    def removeEnd(self):
        self.tail.prev.prev.next = self.tail
        self.tail.prev = self.tail.prev.prev

    def print(self):
        curr = self.head.next
        while curr != self.tail:
            print(curr.value, " -> ")
            curr = curr.next
        print()
```

### Queues

__Queues__ are a data structure similar to Stacks, with the following properties:
Queues support two operations Enqueue (add to the end) and Dequeue (remove from the beginning)

* Queues are __FIFO__ (First In, First Out) unlike Stack's __LIFO__ (Last In, First Out)
* Queues are easily implemented as Linked Lists (but technically can be implemented much more difficultly as an Array)

|Operation               | Big-O Time |
|----------------------- | ---------- |
|Enqueue                 | O(1)       |
|Dequeue                 | O(1)       |

```
class ListNode:
    def __init__(self, value):
        self.value = value
        self.next = None

class Queue:
    def __init__(self):
        self.left = self.right = None

    def enqueue(self, value):
        newNode = ListNode(value)

        # Queue is non-empty
        if self.right:
            self.right.next = newNode
            self.right = self.right.next

        # Queue is empty
        else:
            self.left = self.right = newNode

    def dequeue(self):
        # Queue is empty
        if not self.left:
            return None

        # Remove left node and return value
        value = self.left.value
        self.left = self.left.next
        return value

    def print(self):
        current = self.left
        while current:
            print(current.value, ' -> ', end ="")
            current = current.next
        print()
```

## Two Pointers

## Sliding Window

## Binary Search

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
