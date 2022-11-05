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

<test>Click me test</test>

Something here

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

__Insertionsort__ is a sorting algorithm similar to how we sort playing cards in our hands.
You want to use this when data is almost all sorted for a small number of elements.
If it's a lot of elements, use Quicksort instead.

Worst-case time:   `O(n^2)`
Best-case time:    `O(n)`
Average-case time: `O(n^2)`
Space:             `O(1)`

Code:

```
""" Insertion Sort """

def insertionSort(mylist):
    for index in range(1, len(mylist)):
        print("Index is ", index)  # 1, 2, 3, 4, 5, 6, 7, 8; this is the outer loop

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
    print("Original: ", mylist)
    insertionSort(mylist)
    print("Insertion Sorted: ", mylist)
```

### Merge Sort

__Mergesort__ is an algorithm based on a divide and conquer strategy that
operates well on any type of dataset, large or small.

Worst-case time:   `O(n log n)`
Best-case time:    `O(n log n)`
Average-case time: `O(n log n)`
Space:             `O(n)`

Merge Sort:

```
""" Merge Sort """
def mergeSort(mylist):
    print("Splitting", mylist)

    if len(mylist) > 1:
        mid = len(mylist) // 2
        lefthalf = mylist[:mid]
        print("Left half ", lefthalf)
        righthalf = mylist[mid:]
        print("Right half ", righthalf)

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

    print("Merging", mylist)


if __name__ == '__main__':
    mylist = [54, 26, 93, 17, 77, 31, 44, 55, 20]
    print("Original: ", mylist)
    mergeSort(mylist)
    print("Merge Sorted: ", mylist)
```

### Quick Sort

__Quicksort__ is a an in-place sorting `O(1)` memory algorithm based on a divide
and conquer strategy that is generally more efficient for small datasets or where
the elements are fairly evenly distributed over the range.

Worst-case time:   `O(n^2)`
Best-case time:    `O(n log n)`
Average-case time: `O(n log n)`
Space:             `O(1)`

Code:

```
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
```

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

Example Code:
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
* So what does a binary search tree do? Allows `O(log n)` access to a node.

Example Code:
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

Assuming the tree is roughly balanced, inserting will traverse the `h` of the tree,
which is `log(n)` for a balanced tree.

Example:

* Root node has a value of 4 and you insert a node with value of 6
* Option 1: You can technically make the root node 6, left node is 4, but not the norm
* Option 2: Easier to make the Right Subtree 6, keep Root Node 4

Note: There are more advanced cases of inserting to create a balanced __AVL__ tree.

For removal, we have a couple cases:

* Case 1 (simpler): 0 or 1 child; after we remove this node, what does the subtree look like?
  Say we have a left subtree of 4 -> 3 -> 2 and want to remove 3.
    Check if right pointer; if right pointer is null, then 4's pointer now points to 2
* Case 2 (more difficult): 2 children for the node we remove
  If we delete the node, we lose the entire subtree. Instead, we want to __replace__ the node
  with a leaf node (and take the smallest value of the right subtree OR the largest value of the left subtree)

Worst case scenario is `O log(n)`; need to find the node you're removing/replacing (traverse height once),
then traverse the height again to remove the node.

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

### Depth-First Search

With a sorted array, you can iterate through that array by going left to right.
One algorithm for traversing a tree is __Depth-First-Search (DFS)__, where you go as
deep (height wise) in one subtree as possible before going to another subtree.

Within a tree, you can do many types of traversals (recursively), including:
* __inorder traversal__ to process left to right
* __reverseorder traversal__ to process right to left
* __preorder traversal__
* __postorder traversal__

```
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

# normally you want to go in order (lowest to highest)
def inorder(root):
    if not root:
        return
    inorder(root.left)
    print(root.value)  # or do something here
    inoder(root.right)

# if you want to go in reverse order (highest to lowest)
def reverseorder(root):
    if not root:
        return
    inorder(root.right)
    print(root.value)  # or do something here
    inorder(root.left)

# print/do something before we go to the left subtree
def preorder(root):
    if not root:
        return
    print(root.value)  # or do something here
    preorder(root.left)
    preorder(root.right)

# print/do something after we go to the right subtree
def postorder(root):
    if not root:
        return
    postorder(root.left)
    postorder(root.right)
    print(root.value)  # or do something here
```

### Breadth-First Search (BFS)

__Breadth-First Search (BFS)__ can be applied to any tree, traversing each layer first (instead of depth).
BFS is a little different than DFS (isn't recursive, doesn't go height/depth first).
BFS is also known as level order traversal where after we process a level of a node, we want to
process its children. BFS is `O(n)` to traverse each node.

```
from collections import deque  # a double ended queue


class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def bfs(root):
    queue = deque()

    if root:
        queue.append(root)

    level = 0
    while len(queue) > 0:
        print("level: ", level)
        for i in range(len(queue)):
            current = queue.popleft()
            print(current.value)

            # this order determines if going left to right (or swap if you want right to left)
            if current.left:
                queue.append(current.left)
            if current.right:
                queue.append(current.right)
        level += 1
```

### BST Sets and Maps

You can create an __Ordered Set__ as a tree.
For Python, you can use a `collections.OrderedDict`

For a __Native Tree Map__, Python doesn't have one normally (`SortedDict` is one under the hood).

```
from sortedcontainers import SortedDict

tree_map = SortedDict(
  {'c': 3,
   'a': 1,
   'b': 2
   })
```

## Backtracking

__Backtracking__ is an algorithm to recursively build a solution incrementally, one piece at a time,
removing those solutions that fail to meet our constraints. There are a few types of backtracking algorithms including:

* __Decision Problem__ - searching for a feasible solution
* __Optimization Problem__ - search for the best solution
* __Enumeration Problem__ - find all feasible solutions

Example Problem:
Question: Determine if a path exists from the root of the tree to a leaf node. It may not contain any zeroes

```
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def canReachLeaf(root):

    # If the first/root node is 0, we can't reach anything downstream
    if not root or root.value == 0:
        return False

    if not root.left and not root.right:
        return True
    if canReachLeaf(root.left):
        return True
    if canReachLeaf(root.right):
        return True
    return False

def leafPath(root, path):
    if not root or root.value == 0:
        return False
    path.append(root.value)

    if not root.left and not root.right:
        return True
    if leafPath(root.left, path):
        return True
    path.pop()
    return False
```

## Heap / Priority Queue

### Heap Properties

A __Heap__ / __Priority Queue__ is different than a regular queue in that instead of FIFO,
we can order based off a priority value. What gets popped is based on a priority property,
which can pop the minimum or maximum of the priority value. A Priority Queue uses a Heap under the hood.

The Heap below is a Binary Heap (can be a minimum binary heap or maximum binary heap).

* __Structure__ Property

* A Binary Heap is basically a __Complete Binary Tree__ (no holes except for possibly the last level)
* If there are missing nodes, we first add nodes on the left to right (next available position)

* __Order__ Property

* The Heap's advantage should be finding the min or max quickly
* We want the minimum (or maximum) value out of everything at the root, done in `O(1)`
* We want every value in the left subtree to be greater than the node value and every value on
  the right subtree to be greater than the node value (then done recursively so all descendants are also higher value)
* We can have duplicate values (so same values can be in multiple subtrees)

Heap represented as an Array

* Binary Heaps are drawn as Binary Trees that are connected as pointers, but they're really
  implemented as Arrays (with no zeroth index; the root is index 1).
* Then next row (left to right) is filled into the array, giving us:

```
# leftChild of i = heap[2 *i]
# rightChild of i = heap[(2 * i) + 1]
# parent of i = heap[i // 2]  # Note, we round down
```

### Push and Pop

__Pushing__ to a heap means inserting a value into the heap.
In order to meet the __order__ property, for a minimum heap, we want to make sure the parent is
smaller. If the descendant is smaller, we swap with the parent. You stop when the parent is smaller than the child.
We shift up (__percolate up__) to compare descendant with parent, then swap if needed.

__Popping__ from a heap means removing a value from the heap. We can't just pop a value
in the middle of a heap because we can lose the structure. The trick to maintaining the
order is to:

* Remove the value and replace it with the last item (e.g. say we remove the root, we take the
last value and put that in the root). We now satisfy the Structure property, but not the Order.
* In order to satisfy the Order property, we'll do the opposite of percolating up (and instead
will percolate down). We take the minimum of the children and swap that with our replaced value.
* We recursively percolate down until both children are greater

```
# Min Heap
class Heap:
    def __init__(self):
        self.heap = [0]

    def push(self, value):
        self.heap.append(value)
        i = len(self.heap) - 1

        # Percolate up
        while i > 1 and self.heap[i] < self.heap[i // 2]:
            tmp = self.heap[i]
            self.heap[i] = self.heap[i // 2]
            self.heap[i // 2] = tmp
            i = i // 2

    def pop(self):
        if len(self.heap) == 1:
            return None
        if len(self.heap) == 2:
            return self.heap.pop()

        res = self.heap[1]
        # Move last value to root
        self.heap[1] = self.heap.pop()
        i = 1
        # Percolate down
        while 2 * i < len(self.heap):
            if (2 * i + 1 < len(self.heap) and \
                self.heap[2 * i + 1] < self.heap[2 * i] and \
                self.heap[i] > self.heap[2 * i + 1]):

                # Swap right child
                tmp = self.heap[i]
                self.heap[i] = self.heap[2 * i + 1]
                self.heap[2 * i + 1] = tmp
                i = 2 * i + 1
            elif self.heap[i] > self.heap[2 * i]:
                # Swap left child
                tmp = self.heap[i]
                self.heap[i] = self.heap[2 * i]
                self.heap[2 * i] = tmp
                i = 2 * i
            else:
                break
        return res
```

Pushing and popping is `O(log n)` (from percolating).
We can get the min or max in `O(1)` time (instead of a Binary Search tree's `O(log n)` time.

### Heapify

There is a special algorithm called __heapify__ where you're given any array and
can turn them into a heap in `O(n)` time.

Quick tip: To get the first node that has children, you can take the entire array // 2
and you'll get the earliest parent that has children. What we'll do is go through
every node that has children, then percolate down.

Continue moving to the left (up the tree) and then percolate / swap.

```
def heapify(self, arr):
    # 0-th position is moved to the end
    arr.append(arr[0])

    self.heap = arr
    cur = (len(self.heap) -1) // 2
    while cur > 0:
        # Percolate down
        i = cur
        while 2 * i < len(self.heap):
            if (2* i + 1 < len(self.heap) and \
                self.heap[2 * i + 1] < self.heap[2 * i] and \
                self.heap[i] > self.heap[2 * i + 1]):
                # Swap right child
                tmp = self.heap[i]
                self.heap[i] = self.heap[2 * i + 1]
                self.heap[2 * i + 1] = tmp
                i = 2 * i + 1
            elif self.heap[i] > self.heap[2 * i]:
                # Swap left child
                tmp = self.heap[i]
                self.heap[i] = self.heap[2 * i]
                self.heap[2 * i ] = tmp
                i = 2 * i
            else:
                break
        cur -= 1
```

Heaps are very important for algorithms.

Heapify runs in `O(n)` time.
You can push and pop from the heap in `O(log n)` time.
You can get the min or max in `O(1)` time.

## Hash Sets/Maps

The most important data structure (in real life and leetcode).

__Sets__

E.g. an unduplicated set of keys

__Maps__

Key-value pairs.

Properties:

* Do not allow duplicates
* Does not maintain any type of ordering

__Hash Usage__

| TreeMap   | HashMap    | Operation |
|---------- | ---------- | --------- |
| O(logn)   | O(1)       | Insert    |
| O(logn)   | O(1)       | Remove    |
| O(logn)   | O(1)       | Search    |
| O(n)      | O(nlogn)   | Inorder   |


Example: Count how often a name appears.
```
names = ["alice", "brad", "collin", "brad", "dylan", "kim"]

countMap = {}

for name in names:
    # if countMap does not contain name
    if name not in countMap:
        countMap[name] = 1
    else:
        countMap[name] += 1
```

### Hash Implementation

A HashMap is implemented under the hood as an array.

```
hashmap.put("Alice", "NYC")
hashmap.put("Brad", "Chicago")
hashmap.put("Collin", "Seattle")
```

We take the key value (e.g. "Alice") and then hash that string into an integer (usually ascii representation)
E.g. a simple hashing algorithm could be add up all the characters, then mod with
how much space you have.

```
a = 0
l = 11
... # add all of this up
```

We can then have: total int from hash % index spaces = index space to put the key, value pair

HashMap Example

| Index  | Key, Value       |
| ------ | ---------------- |
| 0      |                  |
| 1      | "Alice", "NYC"   |

__Hashmap Collissions__

After we insert values, we'll check when we get to 50% usage, then double space and
rehash all of the key, value pairs to recompute their new indexes. E.g. "Alice", "NYC" might be in Index 3
instead of Index 1 after we resize. This process is called __rehashing__ the array.

Ideally the size of the array should be a __prime number__ (instead of doubling, just roughly
doubling until it's a prime).

HashMap Example After Resizing

| Index  | Key, Value        |
| ------ | ----------------- |
| 0      |                   |
| 1      | "Alice", "NYC"    |
| 2      |                   |
| 3      | "Brad", "Chicago" |

__Chaining__ vs __Open Addressing__

With __Chaining__, we can create a linked list to store multiple key, value pairs in the
same index. The disadvantage is that we'll need traverse through these values.

With __Open Addressing__, we get the index and if that address is full, we move onto the next
index to see if that address (key, value) is empty until we find an empty one.
This is a naive way of doing open addressing (can cluster when there's a lot of hashmap collisions).
We can do some smarter ways of open addressing (e.g. square the index), etc.


```
class Pair:
    def __init__(self, key, value):
        self.key = key
        self.value = value

class HashMap:
    def __init__(self):
        self.size = 0
        self.capacity = 2
        self.map = [None, None]

    def hash(self, key):
        index = 0
        for c in key:
            index += ord(c)
        return index % self.capacity  # need to mod in case of out-of-bounds

    def get(self, key):
        index = self.hash(key)

        # This implementation is using a naive Open Addressing (instead of Chaining)
        while self.map[index] != None:
            if self.map[index].key == key:
                return self.map[index].value
            index += 1
            index = index % self.capacity
        return None

    def put(self, key, value):
        index = self.hash(key)

        while True:
            if self.map[index] == None:
                self.map[index] = Pair(key, value)
                self.size += 1
                if self.size >= self.capacity // 2:
                    self.rehash()
                return
            elif self.map[index].key == key:
                self.map[index].value = value
                return

            index += 1
            index = index % self.capacity

    def remove(self, key):
        if not self.get(key):
            return

        index = self.hash(key)
        while True:
            if self.map[index].key == key:
                # Removing an element using open-addressing causes a bug,
                # because we may create a hole in the list, and our get() may
                # stop searching early when it reaches this hole.
                self.map[index] = None
                self.size -= 1
                return
            index += 1
            index = index % self.capacity

    def rehash(self):
        # Ideally we want prime numbers for capacity, but this naive way just doubles
        self.capacity = 2 * self.capacity
        newMap = []
        for i in range(self.capacity):
            newMap.append(None)

        oldMap = self.map
        self.map = newMap
        self.size = 0
        for pair in oldMap:
            if pair:
                self.put(pair.key, pair.value)

    def print(self):
        for pair in self.map:
            if pair:
                print(pair.key, pair.value)
```

## Graphs

There's three types of graphs:

* Matrix
* Adjacency Matrix
* Adjacency List

We've already seen different subsets of graphs:

* Linked Lists are a form of graphs.
* Trees are a form of graphs (e.g. Binary Trees).

What makes up a graph?

* We have __nodes__ (aka __verticies__) with __pointers__ (aka __edges__) connecting them together.
* Graphs can have __cycles__
* Graphs follow the formula: Edges <= Verticies ^2

Types of graphs

* __Directed Graphs__ means you can go in one direction (e.g. from Node A to Node B)
* __Undirected Graphs__ mean you can go either direction (e.g. from Node A to Node B OR Node B to Node A)

### Matrix

A __Matrix__ is a __two dimensional array__ that can be used to represent a graph.

Example:

```
grid = [[0, 0, 0, 0],
        [1, 1, 0, 0],
        [0, 0, 0, 1],
        [0, 1, 0, 0]]

print(grid[1][2])  # gives us row 1, column 2 (`0`). Remember starts at row 0, col 0
```

Hint for defining:

* Use `r` and `c` instead of `x` and `y` (easy to get confused with the latter)
* Use `0` for Free and `1` for Blocked
* Most common movements are left, right, up, or down (i.e. common edges)
* We can define movements (edges) however we want
* `0`'s are nodes and the edges are undirected

### Adjacency Matrix

Less common than regular matrixes, these are usually a square matrix (e.g. same as the grid above).

* The dimensions represents the nodes/vertices
* The size is v * v where v is the number of vertices.
* Usually the value is `0` or `1`; this represents if there is/or no edge between the two vertices
* We still have `E = V^2` hold true

```
adjMatrix[v1][v2] = 1 # an edge exists from v1 to v2 (i.e. a directed edge)
adjMatrix[v2][v1] = 1 # an edge exists from v2 to v1 (i.e. another directed edge)
adjMatrix[v2][v3] = 0 # an edge does not exist from v2 to v3 (i.e. not a directed edge)
```

It's rare to use because we need an entire matrix, meaning we have to use `O(v^2)` space
(no matter how many edges we have). We could reduce down this information to `O(v + v)` => `O(v)`
(number of nodes + number of edges).

### Adjacency List

__Adjacency Lists__ are the most common ways of representing graphs (especially during coding interviews).
Example use cases are say a social network (who follows who).

* Similar to Linked Lists and Tree Nodes
* With generic graphs, we can have any number of 'neighbors'/pointers.

```
# Used for Adjacency Lists

class GraphNode:
    def __init__(self, value):
        self.value = value
        self.neighbors = []
```

This is more space efficient (only declaring an array with the pointers we need)

### Matrix DFS

__Matrix Depth First Search (DFS)__

* Think about it visually (matter of choices, start top left)
* Usually recursive
* Usually can move up, left, right, down (be careful about moving out of bounds, set up that first base case)
* Big overlap with DFS and Backtracking

Example Question with DFS (and backtracking):
Count the unique paths from the top left to the bottom right.
A single path may only move along `0`'s and can't visit the same cell more than once (otherwise might get into infinity paths)

```
# Matrix (2D Grid)
grid = [[0, 0, 0, 0],
        [1, 1, 0, 0],
        [0, 0, 0, 1],
        [0, 1, 0, 0]]

# Count paths (backtracking)
def dfs(grid, r, c, visit):
    ROWS, COLS = len(grid), len(grid[0])

    # Don't go out of bounds or if we've been there already (visit)
    if (min(r, c) < 0 or
        r == ROWS or c == COLS or
        (r, c) in visit or grid[r][c] == 1):  # visit is a hash set
        return 0

    # Found a single path, assuming there's at least one valid path
    if r == ROWS - 1 and c == COLS - 1:
        return 1

    # Adding to a hash set is O(1)
    # Other options is to mark as a `1` if you visited already
    # Another option is to create a duplicate of the grid and modify the duplicated grid
    visit.add((r, c))

    count = 0  # how many ways can you reach the result
    count += dfs(grid, r + 1, c, visit)  # goes down first
    count += dfs(grid, r - 1, c, visit)  # goes up
    count += dfs(grid, r, c + 1, visit)  # goes right
    count += dfs(grid, r, c - 1, visit)  # goes left

    visit.remove((r, c))
    return count

print(dfs(grid, 0, 0, set()))
```

Say we have a 4 * 4 matrix; the worst case is 4 choices to 4 choices, etc.
When you follow one of these 4 paths/branches, we end up with `O(4 ^ (r * c))`
The memory complexity is `O(r * c)`.

### Matrix BFS

BFS is a common algorithm that is a little simpler to understand (visually) than DFS and is
also more time efficient in terms of complexity `O(r * c)`.
Instead of going through layers (columns with DFS), we're now going through layers (rows with BFS).

Question: Find the length of the shortest path from the top left of the grid to the bottom right.

```
# Shortest path from top left to bottom right
def bfs(grid):
    ROWS, COLS = len(grid), len(grid[0])
    visit = set()
    queue = deque()
    queue.append((0, 0))
    visit.add((0, 0))

    length = 0
    while queue:
        for i in range(len(queue)):
            r, c = queue.popleft()
            # check if it's the destination
            if r == ROWS -1 and c == COLS - 1:
                return length  # when we reach the result

            # go through all four directions
            neighbors = [[0, 1], [0, -1], [1, 0], [-1, 0]]
            for dr, dc in neighbors:
                # e.g. first pass is dr=0, dc=1

                # check if out of bounds, if we visited already, and if it's an invalid location (1)
                if (min(r + dr, c + dc) < 0 or
                    r + dr == ROWS or c + dc == COLS or
                    (r + dr, c + dc) in visit or grid[r + dr][c + dc] == 1):
                    continue
                queue.append((r + dr, c + dc))
                visit.add((r + dr, c + dc))
        length += 1

print(bfs(grid))
```

### Adjacency List

An __Adjacency List__ is much easier to run an algorithm on instead of a Matrix.
To implement an adjacency list, you can use a GraphNode (value with list of neighbors) or a HashMap.

* Use a Hashamp if the values are unique
* Otherwise, need to use a GraphNode (value with list of neighbors)

```
# GraphNode for adjacency list
class GraphNode:
    def __init__(self, value):
        self.value = value
        self.neighbors = []

# Or use a HashMap
adjList = {"A": [], "B": []}

# Given directed eges, build an adjacency list
edges = [["A", "B"], ["B", "C"], ["B", "E"], ["C", "E"], ["E", "D"]]

adjList = {}  # Using a hashmap for this example

for src, dst in edges:
    if src not in adjList:
        adjList[src] = []
    if dst not in adjList:
        adjList[dst] = []
    adjList[src].append(dst)

```

#### __DFS__ with an __Adjacency List__

DFS with Backtracking: `O(N^V)`, not very efficient (exponential)

```
# Count paths (backtracking)
def dfs(node, target, adjList, visit):
    if node in visit:
        return 0
    if node == target:
        return 1

    count = 0
    visit.add(node)
    for neighbor in adjList[node]:
        count += dfs(neighbor, target, adjList, visit)
    visit.remove(node)

    return count

print(dfs("A", "E", adjList, set()))
```

#### __BFS__ with an __Adjacency List__

`O(V + E)` for time complexity and space complexity of `O(V)` where `V` is the number of vertices

```
# Shortest path from node to target
def bfs(node, target, adjList):
    length = 0
    visit = set()
    visit.add(node)
    queue = deque()
    queue.append(node)

    while queue:
        for i in range(len(queue)):
            current = queue.popleft()
            if current == target:
                return length

            for neighbor in adjList[current]:
                if neighbor not in visit:
                    visit.add(neighbor)
                    queue.append(neighbor)
        length += 1
    return length

print(bfs("A", "E", adjList))

```

Graphs can get more complicated based on priority
(e.g. if say our graph is cities and there's actual weights to the vertices, say miles/distance
between each city)

## 1-D Dynamic Programming

Example Problem: Fibonacci Sequence

### Brute Force with Recursion

```
# Brute Force
def bruteForce(n):
    if n <= 1:
        return n
    return bruteForce(n - 1) + bruteForce(n - 2)

````

### Memoization (aka __Top Down Dynamic Programming__)

Once you calculate something, you can store that result somewhere (that way you
don't create the subtrees that were already calculated). Basically adds caching.

For the cache, you can either use a hashmap or an array.

Takes `O(n)` time and `O(n)` space.

```

# Memoization (an optimization technique to make applications more efficient)
def memoization(n, cache):
    if n <= 1:
        return n
    if n in cache:
        return cache[n]  # here the cache is a hashmap

    # Cache so we don't have to make the same recursive calls
    cache[n] = memoization(n - 1) + memoization(n - 2)
    return cache[n]
```

### Dynamic Programming (aka __Bottom Up Dynamic Programming__)

The true dynamic programming approach (aka __bottom up dynamic programming__)
where we don't use recursion. Instead of top down, we can start at the base case
immediately and calculate up.

Time complexity is `O(n)` and memory is `O(1)` (since we only save the last two
previous values). This is the most efficient way to solve the problem.

```
# Dynamic Programming
def dp(n):
    if n < 2:
        return n

    dp = [0, 1]
    i = 2
    while i <= n:
        tmp = dp[1]
        dp[1] = dp[0] + dp[1]
        dp[0] = tmp
        i += 1
    return dp[1]
```

Can be represented mathematically:
`F(0) =, F(1) = 1, F(n) = F(n-1) + F(n-2)`

The main idea is that we are taking a big problem, then breaking into subproblems
and then solving that subproblem

## 2-D Dynamic Programming

Example Problem: Count the number of unique paths from the top left to the bottom right.
1. You are only allowed to move down or to the right
2. there's no blockers (i.e. every path to the result is the same length)


### Brute Force

Time: `O(n ^ (n + m))`
Space: `O(n + m)`

```
# Brute Force
def bruteForce(r, c, rows, cols):
    if r == row or c == cols:
        return 0
    if r == rows - 1 and c == cols - 1:
        return 1

    return (bruteForce(r + 1, c, rows, cols) +
            bruteForce(r, c + 1, rows, cols)

print(bruteForce(0, 0, 4, 4))
```

### Memoization

Time: `O(n * m)`
Space: `O(n * m)`

```
# Memoization
def memoization(r, c, rows, cols, cache):
    if r == rows or c == cols:
        return 0
    if cache[r][c] > 0:
        return cache[r][c]
    if r == rows - 1 and c == cols -1:
        return 1

    cache[r][c] = (memoization(r + 1, c, rows, cols, cache) +
        memoization(r, c + 1, rows, cols, cache))
    return cache[r][c]

print(memoization(0, 0, 4, 4, [[0] * 4 for i in range(4)]))
```

### Dynamic Programming

Time: `O(n * m)`
Space: `O(m)` where m is the number of columns

```
# Dynamic Programming
def dp(rows, cols):
    prevRow = [0] * cols

    for r in range(rows -1, -1, -1):
        curRow = [0] * cols
        curRow[cols - 1] = 1
        for c in range(cols -2, -1, -1):
            curRow[c] = curRow[c + 1] + prevRow[c]
        prevRow = curRow
    return prevRow[0]
```

## Bit Manipulation

__Bit Manipulation__ contains:

* AND
* OR
* XOR

Bit Operations:

```
# AND - both bits need to be 1, otherwise will be 0
n = 1 & 1

# OR - just one bit needs to be 1, otherwise will be 0 if none are
n = 1 | 0

# XOR - exclusive OR, result will be 1 ONLY IF ONE of the bits is 1 (not both, or none)
n = 0 ^ 1

# NOT (negation) - opposite
n = ~n

# Bit Shifting
n = 1
n = n << 1
n = n >> 1

### Truth Tables

## AND

| AND |
| 0 & 0 | 0 |
| 0 & 1 | 0 |
| 1 & 0 | 0 |
| 1 & 1 | 1 |

## OR

| OR |
| `0 | 0` | 0 |
| `0 | 1` | 1 |
| `1 | 0` | 1 |
| `1 | 1` | 1 |

## XOR

| XOR |
| 0 ^ 0 | 0 |
| 0 ^ 1 | 1 |
| 1 ^ 0 | 1 |
| 1 ^ 1 | 0 |

### Bases (e.g. Base 2, Base 10)

Exponents

The exponent of a number shows how many times the number is multiplied by itself.
The zero property of exponents is applied when the exponent of any base is 0.

#### Base 10

`1011` means 10^0 for 1, 10^1 for 10 (the second digit), 10^2 for 0 (the third digit), and 10^4 for 1 (fourth digit).

For base 10:
    5     4     3     2
    10^3  10^2  10^1  10^0
    1000s 100s  10s   1s

Bit Shifting for Base 10

Basically just multiplies by 10

e.g. 5432 would add a 0, so it would be 54320 (i.e. multiplies by 10)

#### Base 2 (aka __Binary__)

`1011` means that the first digit on the right is 1. The next digit is the presence of 2 (another 1).
The third digit is the presence of 4. The next digit is the presence of 8.

It's basically 2^0 for the first right digit, 2^1 for the next digit, 2^2 for the next digit, 2^4 for next, 2^8 next.

For binary (base 2):
    1    0    1    1
    2^3  2^2  2^1  2^0

Bit Shifting for Base 2

Shift left by one, e.g. `001 << 1` bit shift to the left would be `010` (i.e. 1 * 2)
* If we shift left by 1 again, we now have `100`. We're multiplying by 2 again
* If we shift left again `100 << 1`, the `1` drops off and we have `000`.

Shift right by one, e.g. `100 >> 1` bit shift to the right would be `010` (i.e. divide by 2)
* If we shift right by 1, we now have `010 = 50`. We're dividing by 2.
* If we have an odd number, we round down

Code:

```
# Counting Bits
def countBits(n):
    count = 0
    while n > 0:
        if n & 1 == 1:  # AND
            count += 1
        n = n >> 1  # same as n // 2
    return count

# 23 = 10111
print(countBits(23))
```


## Advanced Algorithms

### Overview

Arrays

* Kadane's Algorithm
* Sliding Window Fixed Size
* Sliding Window Variable Size
* Two Pointers
* Prefix Sums

Linked Lists

* Fast and Slow Pointers

Trees

* Trie
* Union-Find
* Segment Tree
* Iterative DFS

Heaps

* Two Heaps

Graphs

* Dijkstra's
* Prim's
* Kruskal's

Dynamic Programming

* 0/1 Knapsack
* Unbounded Knapsack
* LCS
* Palindromes

### Arrays

#### Kadane's Algorithm

Question: Given an array of positive or negative numbers, return a non-empty (contiguous) subarray with the largest sum.

Brute Force

```
# O(n^2)

def bruteForce(nums):
    maxSum = nums[0]

    for i in range(len(nums)):
        curSum = 0
        for j in range(i, len(nums)):
            curSum += nums[j]
            maxSum = max(maxSum, curSum)

    return maxSum
```

Kadane's Algorithm
```
# Kadane's Algorithm: O(n)
def kadanes(nums):
    maxSum = nums[0]
    curSum = 0

    for n in nums:
        curSum = max(curSum, 0)
        curSum += n
        maxSum = max(maxSum, curSum)
    return maxSum
```

Kadane's Algorithm has a lot of overlap under the Sliding Window pattern.

Sliding Window
```
# Return the left and right index of the max subarray sum,
# Assuming there's exactly one result (no ties).
# Sliding window variation of Kadane's: O(n)

def slidingWindow(nums):
    maxSum = nums[0]
    curSum = 0
    maxL, maxR = 0, 0  # positions of window with the maxSum
    L = 0

    for R in range(len(nums)):
        if curSum < 0:
            curSum = 0
            L = R

        curSum += nums[R]
        if curSum > maxSum:
            maxSum = curSum
            maxL, maxR = L, R

    return [maxL, maxR]
```

#### Sliding Window Fixed Size

Question: Given an array, return true if there are two elements within a window of k that are equal

Example 1:
```
k = 2
array = [1, 2, 3, 2, 3, 3]
check 1,2
check 2,3
check 3,2
check 3,3 returns True
```

CheckKNearbyDuplicatesBruteForce
```
# Check if array contains a pair of duplicate values,
# where the two duplicates are no farther than k poisitions from
# each other (i.e. arr[i] == arr[j] and abs(i -j) <= k).
# O(n * k)
def checkKNearbyDuplicatesBruteForce(nums, k):
    for L in range(len(nums)):
        for R in range(L + 1, min(len(nums), L + k)):
            return True
    return False
```

We use a hashset to help optimize the solution by adding the window to the hashset (and removing anything outside the window).
CheckKNearbyDuplicatesOptimized
```
# Same problem using a sliding window
# O(n)
def checkKNearbyDuplicatesOptimized(nums, k):
    window = set()  # Cur window of size <= k
    L = 0

    for R in range(len(nums)):
        if R - L + 1 > k:
            window.remove(nums[L])
            L += 1
        if nums[R] in window:
            return True
        window.add(nums[R])

    return False
```

#### Sliding Window Variable Size


Example 1:

Question: Find the length of the longest subarray, with the same value in each position

```
Array = [4, 2, 2, 3, 3, 3]
4 = L = R
Increment R, check if they're the same value (4 and 2), they're not
Increment L until L = R (both at 2)
Increment R until it's a new value
...
```

longestSubarray
```
# Find the length of longest subarray with the same
# value in each position
# O(n)
def longestSubarray(nums):
    length = 0
    L = 0

    for R in range(len(nums)):
        if nums[L] != nums[R]:
            L = R
        length = max(length, R - L + 1)
    return length
```

Example 2:

Question: Find the minimum length subarray, where the sum is greater than or equal to the target.
Assume all values are positive.

```
Target = 6
# Find length of minimum size subarray where the sum is
# greater than or equal to the target:
# O(n)
def shortestSubarray(nums, target):
    L, total = 0, 0
    length = float("inf")

    for R in range(len(nums)):
        total += nums[R]
        while total >= target:
            length = min(R - L + 1, length)
            total -= nums[L]
            L += 1
    return 0 if length == float("inf") else length

```

#### Two Pointers

Example 1:

Question: Check if an array is a palindrome

```
# Given a string of characters, return true if it's a palindrome,
# return false otherwise
# O(n)
def isPalindrome(word):
    L, R = 0, len(word) - 1
    while L < R:
        if word[L] != word[R]:
            return False
        L += 1
        R -= 1
    return True
```

Example 2:

Question: Given a sorted input array, return the two indices of two elements which
sum up to the target value. Assume there's exactly one solution.

```
Target = 7
Array = [-1, 2, 3, 4, 8, 9]

# Given a sorted array of integers, return the indices
# of two elements (in different positions) that sum up to the
# target value. Assume there is exactly one solution.
# O(n)

def targetSum(nums, target):
    L, R = 0, len(nums) - 1
    while L < R:
        if nums[L] + nums[R] > target:
            R -= 1
        elif nums[L] + nums[R] < target:
            L += 1
        else:
            return [L, R]

```

#### Prefix and Postfix Sums

Prefixes are contiguous blocks that start at the beginning.

```
array = [2, -1, 3, -3, 4]

Example Prefixes:
[2]
[2, -1]
[2, -1, 3]
```

A postfix would look like:
```
[4]
[-3, 4]
[3, -3, 4]
```

Example 1:

Question: Given an array of values, design a data structure that can query the sum
of a subarray of the values

The idea is to precompute the work and save that data so we can use it in our calculations later.

```
class PrefixSum:

    def __init__(self, nums):
        self.prefix = []
        total = 0
        for n in nums:
            total += n
            self.prefix.append(total)

    def rangeSum(self, left, right):
        preRight = self.prefix[right]
        preLeft = self.prefix[left - 1] if left > 0 else 0  # handle out of bounds
        return (preRight - preLeft)
```

### Linked Lists

#### __Fast and Slow Pointers__ (aka __Floyd's tortoise and hare__) algorithm

Example 1:

Question: Find the middle of a linked list

Can just technically count the length and then get the middle in O(n).

The idea is that we have two pointers, a fast and a slow that we initiate both at the front of the linked list.
The fast pointer increments by 2 positions while the slow pointer increments by 1 position.
If the fast pointer reaches the end of the linked list, the slow pointer should be in the middle of the linked list,
at least if there's an odd number of items. If there's an even number, then the fast pointer hits null
and the slow pointer will be at the midway point (assuming we use the latter on a tie; if we want to
use the earlier value on a tie, then just initialize the faster point one ahead of the slow pointer).

```
# Find the middle of a linked list with two pointers
# Time: O(n), Space: O(1)

def middleOfList(head):
    slow, fast, = head, head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow
```

Example 2: Linked List with a cycle

Question: Determine if a linked list has a cycle

We can use a HashSet to add the pointer that we visited into the HashSet, but that costs
O(n) memory space.

If the slow pointer and fast cycle intercept, then it means there is a cycle (i.e. a loop/circle).

```
# Determine if the linked list contains a cycle
# Time: O(n), Space: O(1)

def hasCycle(head):
    slow, fast = head, head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False
```

Example 3:

Question: Determine if a linked list has a cycle and return the beginning of the cycle if there is (or None if no cycle)

```
# Determine if the linked list contains a cycle and
# return the beginning of the cycle, otherwise return null.
# Time: O(n), Space: O(1)
# 2 * slow = fast
# if there is a cycle, slow and fast pointers will intersect at some arbitrary location

def cycleStart(head):
    slow, fast = head, head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break

    if not fast or not fast.next:
        return None

    slow2 = head
    while slow != slow2:
        slow = slow.next
        slow2 = slow2.next
    return slow
```

### Tree

#### Trie (Prefix Tree)

A __trie__ (aka __prefix tree__) is a tree of characters (e.g. `a-z`, `A-Z`). It's a data structure where you can:

* Insert words at `O(1)` (can also be size of the word at `O(n)` where n is the size of the word)
* Search words at `O(1)`

We can use a HashSet, but it doesn't let you do a __search prefix__ in `O(1)`
With a hashmap, we can search for the word "apple", but we can't find the prefix of "ap"; we would have to
search through every word.

We have a single root node that is empty, then there are children of the characters (e.g. `a-z`). Each of those
children have another set of characters (e.g. `a-z`). So the word 'apple' would be 'a', 'p', 'p', 'l', 'e' with
each character as a TrieNode. To keep track of what children are in a TrieNode, we use a hashmap.

```
class TrieNode:
    def __init__(self):
        self.children = {}
        self.word = False  # boolean value to determine if it's the end of the word we're looking for

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        """ If the character is not there, then create a new TrieNode """
        curr = self.root
        for c in word:
            if c not in curr.children:
                curr.children[c] = TrieNode()
            curr = curr.children[c]
        curr.word = True

    def search(self, word):
        curr = self.root
        for c in word:
            if c not in curr.children:
                return False
            curr = curr.children[c]
        return curr.word

    def startsWith(self, prefix):
        curr = self.root
        for c in prefix:
            if c not in curr.children:
                return False
        return True
```

#### Union-Find (aka Disjoint sets)

__Union-Find__ (aka __Disjoint sets__) is a tree data structure, but can be applied to any generic graphs.

Purpose:

* The strenght of the union-find is that it can work with disjointed sets (e.g. two nodes connected, another
  two nodes connected, but the entire graph is not connected to each other)
* Union-find is used to determine if there are any cycles in a graph or count the number of connected components
* You can usually use a __DFS__ so that's why it's pretty rare to see Union-Find (though sometimes Union-Find is more efficient)
* In the cases where we implement path compression and union by rank, we get close to `O(nodes * 1)` for find operations and `O(1)` for union

Implementation:

* Union Find is a __Forest of Trees__; we assume that all nodes are disconnected to start
* We take one of the nodes at a time, then set it as the parent of the other node
* We _union trees by rank (height)_ (smaller tree gets added as a child to the larger tree, connect the smaller tree to the root)
* We can't union already connected components
* `find` function is normally `O(n)`, but can be `O(log n)` if we implement path compression or union by rank
* if we implement path compression AND union by rank for `find` function, we get the __Inverse Ackermann__ time (close to `O(1)`)

Example 1:

```
nodes = [1, 2, 3, 4]
edges = [[1,2], [4,1], [2,4]]

# all nodes are disconnected
# can randomly go to any node


class UnionFind:
    def __init__(self, n):
        self.par = {}  # keep track of the parent, can use as a hash or array
        self.rank = {}  # by rank we mean the 'height', we want our tree to be as small as possible (more efficient for find)

        for i in range(1, n+ 1):
            self.par[i] = i  # initialize the parent as the root itself
            self.rank[i] = 0  # set the rank / height to 0 as a default, can also be 1

    def find(self, n):
        """ Given some node, we want to find the parent (i.e. the root parent) """
        p = self.par[n]
        while p != self.par[p]:
            self.par[p] = self.par[self.par[p]]  # path compression; shorten chain by setting the parent to it's grandparent
                                                 # in case we run find again
            p = self.par[p]
        return p

    def union(self, n1, n2):
        """ Union the two nodes together """
        p1, p2 = self.find(n1), self.find(n2)
        if p1 == p2:  # if the root parents are the same, cannot union since they're the same component
            return False

        # union by rank, aka union by height
        if self.rank[p1] > self.rank[p2]:  # p1 higher height than p2, p1 should be parent
            self.par[p2] = p1
        elif self.rank[p1] < self.rank[p2]:  # p2 is higher height than p1, p2 should be parent
            self.par[p1] = p2
        else:  # heights are equal
            self.par[p1] = p2
            self.rank[p2] += 1  # need to increase the height by one
        return True
```

#### Segment Tree

Note: not as common in coding interviews
Say we're given an array of values and we want to support two main operations:

* update(index, value)
* queryRange(L, R) and get the sum

Approach 1 (with an Array):

* update(index, value) is `O(1)`
* queryRange(L, R) is `O(n)`

Approach 2 (with a Segment Tree):

* update(index, value) is `O(log n)`  # <- note this gets slower than the array
* queryRange(L, R) is `O(log n)`  # <- note this is faster than the array

How does this work?

* We take the array and break it up into segments
* The root node would represent the range for the entire array; left half goes to left node, right half goes to right node
* This is where if we update a value, we have to update multiple nodes

Example 1:

```
Index   0 1 2 3 4 5
Values  5 3 7 1 4 2

               [0,5]
                22
              /    \
        [0,2]       [3,5]
         15           7
        /    \      /   \
    [0,1]   [2,2] [3,4]  [5,5]
      8       7     5      2
    /    \        /    \
[0,0]   [1,1]   [3,3]  [4,4]
  5       3       1      4
```

```
class SegmentTree:
    def __init__(self, total, L, R):  # e.g. total is the 22 at the root node
        self.sum = total  # e.g. 22 at the root [0,5] (total of everything added up)
        self.left = None  # pointer to the left child
        self.right = None  # pointer to the right child
        self.L = L  # references index of the left boundary of the node e.g. 0 of the [0,5]
        self.R = R  # references index of the right boundary of the node e.g. 5 of the [0,5]

    # O(n)
    @staticmethod
    def build(nums, L, R):
        """ Build a Segment Tree """
        if L == R:
            return SegmentTree(nums[L], L, R)

        M = (L + R) // 2
        root = SegmentTree(0, L, R)
        root.left = SegmentTree.build(nums, L, M)  # build left side of the segment tree
        root.right = SegmentTree.build(nums, M + 1, R)  # build right side of the segment tree
        root.sum = root.left.sum + root.right.sum  # calculate the sum of the root node
        return root

    # O(log n)
    def update(self, index, value):
        """

        """
        if self.L == self.R:
            self.sum = value
            return

        M = (self.L + self.R) // 2  # similar to how we built the segment tree, find where this value is
        if index > M:
            self.right.update(index, value)
        else:
            self.left.update(index, value)
        self.sum = self.left.sum + self.right.sum

    # O(log n)
    def rangeQuery(self, L, R):
        """
        think of ranges as a number line. the entire range can be one of the following scenarios:
          * left only
          * right only
          * left and right
        we go through each value and keep popping back up the actual sum/total
        """
        if L == self.L and R == self.R:
            return self.sum

        M = (self.L + self.R) // 2
        if L > M:
            return self.right.rangeQuery(L, R)
        elif R <= M:
            return self.left.rangeQuery(L, R)
        else:
            return (self.left.rangeQuery(L, M) +
                    self.right.rangeQuery(M + 1, R))
```

#### Iterative DFS

Note: Recursive DFS are easier; use that if you can.
We can implement DFS on a binary tree with recursion, but we can also do this as an __Iterative DFS__.
We can do this three ways and we'll do some type of operation (print in the example):

* inorder
* preorder
* postorder

Example 1:
```
    1
   / \
  2   3
 /      \
4        5

```


##### Inorder

We process the children node first. We go left and keep going left until we can't anymore (null node).
On a recursive solution, we would just return (pop back up to the previous node of the call stack).
When we do this iteratively, we do the same thing (not a call stack, but just create a stack data structure
and save the nodes onto the stack).

```
Add to the stack

Stack
 1

Stack
 2
 1

Stack
 4
 2
 1

Reach a null node, then pop from the stack

Stack
  popped 4 (next step is to keep popping because nothing on the right)
 2
 1

Stack
  popped 4
  popped 2 (next step is to keep popping because nothing on the right)
 1

Stack
  popped 4
  popped 2
  popped 1 (start traversing the right subtree)
  3

Stack
  popped 4
  popped 2
  popped 1
  popped 3
  5

Stack
  popped 5, no more nodes to traverse, can stop algorithm
```


##### Preorder

We print or process the current node BEFORE we process the children node.

Code:
```
# Definition for a binary tree node
class TreeNode:
    def __init__9self, val, left, right):
        self.val = val
        self.left = left
        self.right = right

# Time and space: O(n), really O(height) of the tree
def inorder(root):
    """ We're given the root node and we create our own stack """
    stack = []
    curr = root

    while curr or stack:  # if our curr pointer is null and stack is empty, we reached the end
        if curr:
            stack.append(curr)  # save node by pushing to the stack
            curr = curr.left  # traverse left
        else:
            curr = stack.pop()  # pop back to the parent node
            print(curr.val)
            curr = curr.right  # traverse right

# Time and space: O(n)
def preorder(root):
    """
    We print or process the current node BEFORE we process the children node
    We process the left subtree immediately (e.g. 1, 2, 4) then right subtree after (3, 5)
    At the root node, add left and right node to the stack, then keep traversing left nodes and push to the stack.
    """
    stack = []
    curr = root
    while curr or stack:
        if curr:
            print(curr.val)
            if curr.right:
                stack.append(curr.right)
            curr = curr.left
        else:
            curr = stack.pop()

# Time and space: O(n)
def postorder(root):
    """
    We have a stack and a visit flag stack
    * Start with adding the root node (1) and visit flag (False)
    * Then add next level (2, False) and (3, False)
    * Now add the original/root node (1, True) but with a True since it's been visited
    * We add 3 to the stack first (False), then 2 to the stack (False)
    * We don't maintain our pointer, we just pop from the stack
    * We then visit 2 and add that to the stack (2, True), then add its children to the stack
    * We add the right child (Null) to the stack (visit = False)
    * We then add 4 to the stack (visit = False)
    * When we pop a value with a visit = True, we print it because all of its children have been added
    * etc
    """
    stack = [root]
    visit = [False]
    while stack:
        curr, visited = stack.pop(), visit.pop()  # going to be the same size stacks
        if curr:
            if visited:
                print(curr.val)  # if visited already, we already added its descendants
            else:
                stack.append(curr)  # take the current node and add it to the stack
                visit.append(True)

                # important to add right child first so left child is popped first
                stack.append(curr.right)
                visit.append(False)  # we set False because we haven't added its children yet
                visit.append(curr.left)
                visit.append(False)  # we set False because we haven't added its children yet
```

### Two Heaps

Example 1 (Basic):

Two heaps are useful when you want to find the median.
```
Odd
[4, 7, 3, 5, 1]
Median would be the middle value = 4

Even
[1, 3, 4, 5]
Median would be 3 + 4 / 2 = 3.5
```

Example 1 (Stream):
So instead of getting all of those values at once, say we get a stream of values:

```
[4]
[4, 7]
[4, 7, 10]
```

If we're inserting a new value: `O(n)` to insert into a sorted order
To `getMedian` is an `O(1)` operation then.

Question: Implement a Median finder, where new values are inserted into the set,
and you have to getMedian from that set.

We want to insert with `O(log n)` and getMedian with `O(1)`.

Solution:

* We'll have two heaps: Small (`maxHeap`) and Large (`minHeap`).
* We insert all the small values into small heap, large values into large heap
* When we insert a single value, can just put to the small heap
* We want all values in the small heap <= large heap
* We want the number of values from both heaps to roughly equal
* We take the values from both heaps, add them together and divide by 2

```
import heapq


class Median:
    def __init__(self):
        self.small, self.large = [], []

    def insert(self, num):
        # Push to the max heap and swap with min heap if needed
        heapq.heappush(self.small, -1 * num)
        if (self.small and self.large and (-1 * self.small[0]
            val = -1 * heapq.heappop(self.small)
            heapq.heappush(self.large, val)

        # Handle uneven size
        if len(self.small) > len(self.large) + 1:
            val = -1 * heapq.heappop(self.small)
            heapq.heappush(self.large, val)
        if len(self.large) > len(self.small) + 1:
            val = heapq.heappop(self.large)
            heapq.heappush(self.small, -1 * val)

    def getMedian(self):
        if len(self.small) > len(self.large):
            return -1 * self.small[0]
        elif len(self.large) > len(self.small):
            return self.large[0]

        # Even # of elements, return avg of two middle nums
        return (-1 * self.small[0] + self.large[0]) /2
```

### Backtracking

#### Subsets

#### Combinations

#### Permutations

### Graphs

#### Dijkstra's

#### Prim's

#### Kruskal's

#### Topological Sort

### Dynamic Programming

#### 0/1 Knapsack

#### Unbounded Knapsack

#### LCS

#### Palindromes

## Competitive Programming Algorithms and Data Structures

More advanced/competitive programming algorithms can be found [here](https://cp-algorithms.com/)

* __Rabin-Karp algorithm__ for String Matching uses hasing to find an exact match of a pattern in a text.
* Prefix function - __Knuth-Morris-Pratt (KMP)__ searches for occurrences of a 'word' W within a main 'text string' S by
  employing the observation that when a mismatch occurs, the word itself embodies sufficient information to determine
  where the next match could begin
* Sieve of Eratosthenes - algorithm for finding all the prime numbers in a segment
* Fenwick Tree - a data structure that can efficiently update elemnts and calculate prefix sums in a table of numbers

