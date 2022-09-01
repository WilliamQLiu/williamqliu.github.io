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

## Recursion

### One-Branch Recursion

n! = n * (n-1) * (n-2) * ... * 1
Example: 5! = 5 * 4 * 3 * 2 * 1
Notice:  5! = 5 * 4!
Notice:  5! = 5 * (5-1)!
Pattern: n! = n * (n-1)!

We took one big problem and turned it into many subproblems.
We work on a problem until we get to the base case (e.g. 1)

Example:
```
# Recursive implementation of n! (n-factorial) calculation
def factorial(n):
    # Base case: n = 0 or 1
    if n <= 1:
        return 1

    # Recursive case: n! = n * (n - 1)!
    return n * factorial(n - 1)
```

## Sorting

### Insertion Sort

### Merge Sort

### Quick Sort

### Bucket Sort

## Binary Search

### Binary Search

__Binary Search__ works on a __sorted__ list of items by repeatedly dividing in half to narrow down your search.

* If it is not in sorted order, you cannot use Binary Search (otherwise will be same as visiting every item)
* We want a boundary that makes up our search space, basically a left pointer and a right pointer
* We want a __middle index__ that is `left pointer + right pointer / 2` (and round down) to get the middle index

Example:
Guess a number from 1 -100, you pick 50
You know it's lower than 50, so you pick 25 next
You know it's higher than 25, so you pick 12, etc.

```
arr = [1, 3, 3, 4, 5, 6, 7, 8]

# Python impelemntation of Binary Search
def binarySearch(arr, target):
    L, R = 0, len(arr) - 1

    while L <= R:
        mid = (L + R) // 2

        if target > arr[mid]:
            L = mid + 1
        elif target < arr[mid]:
            R = mid - 1
        else:
            return mid
    return -1
```

|Operation               | Big-O Time |
|Memory                  | O(1)       |
|Time                    | log(n)     |

### Search Range

__Search Range__ is a slight variation of the Binary Search (e.g. not given a sorted array)
You might not be given an array of 1-100 or given a target (e.g. 10).
You might be given a search range where n is the secret number that you have to guess.

```
# low = 1, high = 100

# Binary search on some range of values
def binarySearch(low, high):

    while low <= high:
        mid = (low + high) // 2

        if isCorrect(mid) > 0:
            high = mid - 1
        elif isCorrect(mid) < 0:
            low = mid + 1
        else:
            return mid
    return -1

# Return 1 if n is too big, -1 if too small, 0 if correct
def isCorrect(n):
    if n > 10:
        return 1
    elif n < 10:
        return -1
    else:
        return 0
```

## Trees

### Binary Trees

__Binary Trees__ are similar in structure to __Linked Lists__ in that there are Nodes, which
can hold values (e.g. numbers, strings), and two (left and right due to binary) nodes that are typically pointed down.

* The initial Node is the __Root Node__.
* There are relationships called __Parent Nodes__ and __Children Nodes__.
* Pointers that do not have any children are __Leaf Nodes__ (Guaranteed to have)
* Binary Trees are not allowed to have __Cycles__ (unlike a Linked List), meaning two Adjacent nodes cannot connect to each other
* Two adjacent nodes are __Sibling Nodes__.
* All nodes have to be connected (can't be floating randomly somewhere)
* All nodes have a __Height__ property. We measure the __Height__ based on the children nodes (including it and all of its __Descendants__)
* __Ancestor__ Nodes are any Node going up that node. The __Root Node__ does not have any Ancestor nodes.
* The __Depth__ is the opposite of __Height__, which is basically the Height going up instead of down.

```
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None
```

### Binary Search Tree (BST)

__Binary Search Tree__ (BST) is a special type of Binary Tree that has a __sorted property__.
These are not sorted like an array.
* Every single node in the left subtree has to be less than the root/current node.
* Every single node in the right subtree has to greater than the root/current node.
* Binary Search Trees usually do not contain duplicates
* The definition of a BST makes it easy to do __recursion__ (i.e. one-branch recursion, go in one direction)
* So what does a binary search tree do? Allows `log(n)` access to a node.
```
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def search(root, target):
    # Need a root
    if not root:
        return False

    if target > root.value:
        return search(root.right, target)
    elif target < root.value:
        return search(root.left, target)
    else:
        return True
```

Why create a Binary Search Tree when we have Sorted Arrays?
  If you want to add or remove a value from a sorted array, it's (`O(n)`) due to shifting over values.
  The advantage of a Binary Search Tree is that inserting and deleting values can also be `log(n)`


Assuming that a Binary Search Tree is __roughly balanced__ (for every single subtree, the height
of the left and right might differ by 0 or 1).

|Operation               | Big-O Time  |
|----------------------- | ----------- |
|Read/Write i-th element | log(n), O(h)|


If the Binary Search Tree is not balanced (e.g. one subtree has a height of 10 and another subtree has has height of 2)
|Operation               | Big-O Time |
|----------------------- | ---------- |
|Read/Write i-th element | O(n)       |

### Binary Search Tree Insert and Remove

Assuming a tree is roughly balanced, inserting is `O(h)` or `log(n)`.

```
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

# Insert a new node and return the root of the BST
def insert(root, value):
    if not root:
        return TreeNode(value)

```

### BST Insert and Remove

Assuming the tree is roughly balanced, inserting will traverse the `h` of the tree.

Example:
* Root node has a value of 4 and you insert a node with value of 6
* Option 1: You can technically make the root node 6, left node is 4, but not the norm
* Option 2: Easier to make the Right Subtree 6, keep Root Node 4

Note: There are more advanced cases of inserting to create a balanced __AVL__ tree.

```
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

# Insert a new node and return the root of the BST
def insert(root, value):
    if not root:
        return TreeNode(value)

    if value > root.value:
        root.right = insert(root.right, value)
    elif value < root.value:
        root.left = insert(root.left, value)
    return root

# Return the minimum value node of the BST
def minValueNode(root):
    current = root
    while current and current.left:
        current = current.left
    return current

# Remove a node and return the root of the BST
def remove(root, value):
    if not root:
        return None

    if value > root.value:
        root.right = remove(root.right, value)
    elif value < root.value:
        root.left = remove(root.left, value)
    else:
        if not root.left:
            return root.right
        elif not root.right:
            return root.left
        else:
            minNode = minValueNode(root.right)
            root.value = minNode.value
            root.right = remove(root.right, minNode.value)
    return root
```

## Two Pointers

## Sliding Window


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
