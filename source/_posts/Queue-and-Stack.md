---
title: Queue and Stack
date: 2019-02-17 17:33:33
tags:
- LeetCode
categories:
- Programming
translations:
  zh-CN: https://youngforest.github.io/2019/02/17/Queue-and-Stack/
  en: https://youngforest.github.io/en/2019/02/17/Queue-and-Stack/
---
Today let's study two important data structures together: queue and stack.
This article is based on LeetCode's Explore tutorial, [Introduction to Data Structure - Queue & Stack](https://leetcode.com/explore/featured/card/queue-stack/).

## Introduction

The most commonly used collection is the array, and its most commonly used data-access operation is random access, usually called subscript access in C++.
But sometimes we want to restrict the order in which data is processed. The most common restrictions are First in first out and Last in first out. They correspond to two data structures: Queue and Stack.

We will study queue and stack from three angles: definition, implementation, and the built-in operations of each data structure.
Learning goals:
1. Understand the principles of FIFO and LIFO data-processing order;
2. Manually implement the data structures;
3. Become familiar with the built-in Queue and Stack in the language;
4. Solve basic Queue-related problems, especially BFS;
5. Solve basic Stack-related problems;
6. Understand how the system stack helps you solve DFS and other recursive problems.

## Queue: First-in-first-out Data Structure

### Definition

The most common metaphor for First in first out is waiting in line, that is, a queue. The person who enters the queue earliest is served earliest.
So a queue has only two modify methods:
- enqueue
- dequeue

### Implementation

Queue is not a primitive data structure. We can implement it using a built-in array. In C++, `queue` is a container adapter, not a real container; internally, it is actually a `deque`.

Implementation example:
```cpp
#include <iostream>

class MyQueue {
    private:
        // store elements
        vector<int> data;       
        // a pointer to indicate the start position
        int p_start;            
    public:
        MyQueue() {p_start = 0;}
        /** Insert an element into the queue. Return true if the operation is successful. */
        bool enQueue(int x) {
            data.push_back(x);
            return true;
        }
        /** Delete an element from the queue. Return true if the operation is successful. */
        bool deQueue() {
            if (isEmpty()) {
                return false;
            }
            p_start++;
            return true;
        };
        /** Get the front item from the queue. */
        int Front() {
            return data[p_start];
        };
        /** Checks whether the queue is empty or not. */
        bool isEmpty()  {
            return p_start >= data.size();
        }
};

int main() {
    MyQueue q;
    q.enQueue(5);
    q.enQueue(3);
    if (!q.isEmpty()) {
        cout << q.Front() << endl;
    }
    q.deQueue();
    if (!q.isEmpty()) {
        cout << q.Front() << endl;
    }
    q.deQueue();
    if (!q.isEmpty()) {
        cout << q.Front() << endl;
    }
}
```

### Circular Queue

In the previous implementation, the memory before `p_start` is wasted. To make full use of it, we can internally reuse the array in a circular way.

Circular Queue is also called a "Ring Buffer".

Ring Buffer implementation:
```cpp
class MyCircularQueue {
    vector<int> data;
    int head;
    int size;
public:
    /** Initialize your data structure here. Set the size of the queue to be k. */
    MyCircularQueue(int k) {
        data.insert(data.begin(), k, 0);
        head = 0;
        size = 0;
    }
    
    /** Insert an element into the circular queue. Return true if the operation is successful. */
    bool enQueue(int value) {
        if (isFull())
            return false;
        
        data[(head + size) % data.size()] = value;
        size++;
        return true;
    }
    
    /** Delete an element from the circular queue. Return true if the operation is successful. */
    bool deQueue() {
        if (isEmpty())
            return false;
        
        head = (head + 1) % data.size();
        size--;
        return true;
    }
    
    /** Get the front item from the queue. */
    int Front() {
        if (isEmpty()) return -1;
        return data[head];
    }
    
    /** Get the last item from the queue. */
    int Rear() {
        if (isEmpty()) return -1;
        return data[(head + size - 1) % data.size()];
    }
    
    /** Checks whether the circular queue is empty or not. */
    bool isEmpty() {
        return size == 0;
    }
    
    /** Checks whether the circular queue is full or not. */
    bool isFull() {
        return size == data.size();
    }
};

/**
 * Your MyCircularQueue object will be instantiated and called as such:
 * MyCircularQueue* obj = new MyCircularQueue(k);
 * bool param_1 = obj->enQueue(value);
 * bool param_2 = obj->deQueue();
 * int param_3 = obj->Front();
 * int param_4 = obj->Rear();
 * bool param_5 = obj->isEmpty();
 * bool param_6 = obj->isFull();
 */
```

### Applications of Queue

The most typical application is BFS.
BFS (Breadth-first Search) is generally used to find the shortest distance from a root node to a target node.

Scenarios where BFS is used:
- do traversal
- find the shortest path

Common data structures in these scenarios:
- graph
- tree

In concrete applications, nodes in BFS may be **real nodes** or **states**, and edges may be **real edges** or **state transitions**.

The BFS template must be memorized to improve speed and the chance of being bug-free in interviews and problem solving.

#### template 1

```java
/**
 * Return the length of the shortest path between root and target node.
 */
int BFS(Node root, Node target) {
    Queue<Node> queue;  // store all nodes which are waiting to be processed
    int step = 0;       // number of steps neeeded from root to current node
    // initialize
    add root to queue;
    // BFS
    while (queue is not empty) {
        step = step + 1;
        // iterate the nodes which are already in the queue
        int size = queue.size();
        for (int i = 0; i < size; ++i) {
            Node cur = the first node in queue;
            return step if cur is target;
            for (Node next : the neighbors of cur) {
                add next to queue;
            }
            remove the first node from queue;
        }
    }
    return -1;          // there is no path from root to target
}
```

#### template 2


```java
/**
 * Return the length of the shortest path between root and target node.
 */
int BFS(Node root, Node target) {
    Queue<Node> queue;  // store all nodes which are waiting to be processed
    Set<Node> visited;  // store all the nodes that we've visited
    int step = 0;       // number of steps neeeded from root to current node
    // initialize
    add root to queue;
    add root to visited;
    // BFS
    while (queue is not empty) {
        step = step + 1;
        // iterate the nodes which are already in the queue
        int size = queue.size();
        for (int i = 0; i < size; ++i) {
            Node cur = the first node in queue;
            return step if cur is target;
            for (Node next : the neighbors of cur) {
                if (next is not in used) {
                    add next to queue;
                    add next to visited;
                }
                remove the first node from queue;   
            }
        }
    }
    return -1;          // there is no path from root to target
}
```
1. In each round, the nodes in the queue are waiting to be processed.
2. Each time the outer `while` loop runs, we move one step farther from `root`, so `step++`.

#### template 2

In a graph, it is important to ensure that each node is not visited multiple times. Otherwise, BFS can fall into an infinite loop. At this point, we add a `hashset` to mark whether a node has already been visited.

```java
/**
 * Return the length of the shortest path between root and target node.
 */
int BFS(Node root, Node target) {
    Queue<Node> queue;  // store all nodes which are waiting to be processed
    Set<Node> visited;  // store all the nodes that we've visited
    int step = 0;       // number of steps neeeded from root to current node
    // initialize
    add root to queue;
    add root to visited;
    // BFS
    while (queue is not empty) {
        step = step + 1;
        // iterate the nodes which are already in the queue
        int size = queue.size();
        for (int i = 0; i < size; ++i) {
            Node cur = the first node in queue;
            return step if cur is target;
            for (Node next : the neighbors of cur) {
                if (next is not in used) {
                    add next to queue;
                    add next to visited;
                }
            }
            remove the first node from queue;   
        }
    }
    return -1;          // there is no path from root to target
}
```

When can we skip `visited`?
1. You are sure there will be no repeated visits, for example when traversing a tree.
2. You intentionally want to add a node to the queue multiple times.


## Stack

When I mention stack, I think of Jay Chou's *Qilixiang*. In my head, one line becomes: my stack overflowed like rainwater.

In a LIFO data structure, the most recently added element is processed first. In a stack, the operation for adding an element is called `push`, and the operation for removing an element is called `pop`. Although C++ queue operations use the same names, in most languages, `push` and `pop` are stack-specific.

Like queue, most languages provide a built-in stack library. You do not need to reinvent the wheel; you only need to know the common stack operations, including `push`, `pop`, and `top` (getting the top element of the stack).

### Applications of Monotonic Stack

https://leetcode.com/explore/featured/card/queue-stack/230/usage-stack/1363/

Intuition: maintain a monotonically decreasing stack. Traverse array `T`; when placing an element into the stack, pop all elements in the stack that are smaller than it and compute the corresponding interval.

Time complexity: O(n),
space complexity: O(n).

```cpp
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& T) {
        vector<int> result(T.size(), 0);
        stack<pair<int, int>> s; // temperature, day
        for (int i = 0; i < T.size(); ++i) {
            while (!s.empty() && s.top().first < T[i]) {
                pair<int, int> current = s.top();
                result[current.second] = i - current.second;
                s.pop();
            }
            s.push({T[i], i});
        }
        
        return result;
    }
};
```

### stack and DFS

DFS is one of the important applications of stack. It can be used to find a path from a root node to a target node, though not necessarily the shortest path. DFS is a backtracking algorithm. It only backtracks after reaching the deepest node, then tries other paths.

#### DFS template 1, recursive version

```java
/*
 * Return true if there is a path from cur to target.
 */
boolean DFS(Node cur, Node target, Set<Node> visited) {
    return true if cur is target;
    for (next : each neighbor of cur) {
        if (next is not in visited) {
            add next to visted;
            return true if DFS(next, target, visited) == true;
        }
    }
    return false;
}
```

#### DFS template 2, iterative version

The advantage of the recursive version is that it is easier to implement. The disadvantage is that if the recursion depth is too large, it can cause stack overflow.
At that point, you may want to use BFS or implement DFS with an explicit stack.


```java
/*
 * Return true if there is a path from cur to target.
 */
boolean DFS(int root, int target) {
    Set<Node> visited;
    Stack<Node> stack;
    add root to stack;
    while (s is not empty) {
        Node cur = the top element in stack;
        remove the cur from the stack;
        return true if cur is target;
        for (Node next : the neighbors of cur) {
            if (next is not in visited) {
                add next to visited;
                add next to stack;
            }
        }
    }
    return false;
}
```

The implementation logic is the same as the recursive solution. We simply use a `while` loop and an explicit `stack` to mimic the system stack.

### [Implement Queue using Stacks](https://leetcode.com/explore/featured/card/queue-stack/239/conclusion/1386/)
Implement a queue using stacks.
I remember seeing the same problem in *Cracking the Coding Interview*. It feels like a classic problem. It requires the interviewee to be very familiar with both queue and stack.

Intuition: queue is FIFO and stack is LIFO, so we can implement one queue with two stacks. Define the operation of pouring one stack into another stack as **reversing**. Through **reversing**, we can turn LIFO into FIFO, and we only need to perform the reversing operation when we need to `dequeue`.

```cpp
class MyQueue {
    stack<int> a, b;
public:
    /** Initialize your data structure here. */
    MyQueue() {
        
    }
    
    /** Push element x to the back of queue. */
    void push(int x) {
        a.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        if (b.empty()) {
            while (!a.empty()) {
                b.push(a.top());
                a.pop();
            }
        }
        int ret = b.top();
        b.pop();
        return ret;
    }
    
    /** Get the front element. */
    int peek() {
        if (b.empty()) {
            while (!a.empty()) {
                b.push(a.top());
                a.pop();
            }
        }
        return b.top();
    }
    
    /** Returns whether the queue is empty. */
    bool empty() {
        return a.empty() && b.empty();
    }
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = new MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * bool param_4 = obj.empty();
 */
```

### [Implement Stack using Queues](https://leetcode.com/explore/featured/card/queue-stack/239/conclusion/1387/)

So how do we use FIFO to implement LIFO? The solution may not be that obvious, but it is very simple. Every time we `push`, re-enqueue the existing elements in the queue once, which puts the most recently enqueued element at the first position.

## Summary
DFS and BFS are simple in idea but not easy to implement, especially if you want to implement them bug-free and quickly.
Because they test a wide range of programming fundamentals, interviewers especially like asking these kinds of problems. You must memorize several templates fluently.

Queue and stack are even more fundamental data structures. They appear everywhere in computer science. Although they are often not tested directly, many algorithms rely on them. Being able to handwrite stack and queue, and to use built-in stack and queue fluently, is foundational for every qualified programmer.
