---
title: Recursion
date: 2019-02-11 12:58:52
tags:
- LeetCode
- Algorithm
categories:
- Programming
description: "A structured guide to recursion, recurrence relations, memoization, complexity analysis, call stacks, and tail-recursion optimization."
translations:
  zh-CN: https://youngforest.github.io/2019/02/11/recursion/
  en: https://youngforest.github.io/en/2019/02/11/recursion/
---
This article is based on LeetCode's tutorial [Introduction to Algorithms - Recursion I](https://leetcode.com/explore/learn/card/recursion-i/). Its purpose is to help me become more familiar with the important programming concept of "recursion". If it can also help others, that would be even better.

The structure of this article is exactly the same as LeetCode's tutorial. It is divided into six parts: Introduction, Principles of Recursion, Recurrence Relation, Memoization, Complexity Analysis, and Conclusion.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/recursion/en-hero.webp" alt="A nested workshop unfolds into smaller copies until an amber base-case tile sends results back upward, while side drawers hold repeated results" width="1536" height="864" decoding="async">
</figure>

<!-- more -->

## Introduction

The goal of this card is to answer the following questions:
1. What is recursion? How does recursion work?
2. How do we solve a problem recursively?
3. How do we analyze the time complexity and space complexity of a recursive algorithm?
4. How can we apply recursion in a better way?

## Principles of Recursion

Every time a recursive function calls itself, it transforms the given problem into a subproblem. The recursive process continues until the subproblem can be solved directly without further recursion.

Necessary properties for avoiding infinite recursion in a recursive function:
1. termination conditions (**base cases**)
2. a set of rules (**recurrence relation**) that can reduce all other cases to base cases.

A recursive function can call itself from multiple places.

### Example: Reverse a String
```cpp
void printReverse(const char *str) {
    if (!*str) return;  // base case
    printReverse(str + 1);
    putchar(*str);
}
```

### Recursive Function

If a problem has a recursive solution, we can implement it by following these steps.

We define the problem as something that can be implemented by function `F(x)`, where `X` is the input to the function and also represents the scope of the problem.

Inside function `F(X)`, we implement the following steps:
1. Split the problem into smaller scopes, $x_1 \belong X, x_2 \belong X, ..., x_n \belong X$.
2. Recursively call `F(x_1), F(x_2), ..., F(x_n)` to solve the subproblems of `X`.
3. Finally, process the solutions of the subproblems and combine them into the solution corresponding to `X`.

## Recurrence Relation

Definition: the relationship between the solution of a problem and the solutions of its subproblems.

### Example: Pascal's Triangle

Definition: Pascal's Triangle is a triangular arrangement of numbers. In Pascal's Triangle, the leftmost and rightmost numbers of each row are always 1. For the remaining numbers, each number is the sum of the two numbers directly above it in the previous row.

Expressed mathematically, the recurrence relation is:
$f(i, j) = f(i-1, j-1) + f(i-1, j),$

and the base cases are:
$f(i, j) = 1, if j = 1 or j = i$.

Here, $f(i, j)$ represents the `j`-th number in row `i`.

## Memoization

### Repeated Computation During Recursion

Recursive solutions are often very **intuitive** and **easy to code**. But most of the time, repeated computation during recursion causes performance loss.

**Memoization**
is a general technique for avoiding repeated computation.
Yes, this word is not misspelled. It is not Memorization.

Definition: to avoid repeated computation, we can store intermediate subproblem results in a cache, so that when they are needed again later, they do not need to be recomputed.

Memoization can be implemented with a hashmap. Especially in OOP, decorators can be used to implement generic memoization.

### Example: Fibonacci Number

[Multiple solutions to Fibonacci numbers](https://leetcode.com/articles/climbing-stairs/), including the **Binet's Formula** method and a formula-based method with O(log n) time complexity, are quite impressive.

## Complexity Analysis

The complexity of a recursive algorithm is sometimes not obvious and has to be analyzed through certain patterns.

Tail recursion is a special technique that can reduce recursion stack usage and optimize space complexity to be the same as an iterative algorithm.

### Time Complexity

The time complexity of a recursive algorithm is:
O(T) = R * O(s),
where R is the number of recursive calls, and O(s) is the computational complexity produced by each recursive call.
Generally speaking, R is harder to calculate, while O(s) is analyzed in the same way as the time complexity of a non-recursive algorithm.

With the execution tree technique, we can better analyze the number of recursive calls.
An execution tree is a tree that shows the flow of recursive calls in a concrete case. Each node represents one call, and the value on the node represents the parameter at the time of the call.
The number of nodes in this tree is R.

Special attention is needed for how the execution tree changes when memoization is used.

### Space Complexity

The space usage of a recursive algorithm is mainly divided into two parts:
1. recursion related
2. non-recursion related

#### recursion related

As we know from studying compilers, each function call pushes the following onto the stack:
1. the function return address
2. function parameters
3. local variables of the function

The depth of the function call stack in a recursive algorithm goes from the initial case to the base case.

#### non-recursion related

The space used by global variables is mainly allocated on the heap. For example, memoization needs a hashmap.

### Tail Recursion

Tail recursion is a form of recursion where the recursive call is the final instruction of the recursive function, and there is only one recursive call in the function.

A good example of tail recursion. Note that `non_tail_recursion` still performs computation after the final recursive call.
```java
public class Main {
    
  private static int helper_non_tail_recursion(int start, int [] ls) {
    if (start >= ls.length) {
      return 0;
    }
    // not a tail recursion because it does some computation after the recursive call returned.
    return ls[start] + helper_non_tail_recursion(start+1, ls);
  }

  public static int sum_non_tail_recursion(int [] ls) {
    if (ls == null || ls.length == 0) {
      return 0;
    }
    return helper_non_tail_recursion(0, ls);
  }

  //---------------------------------------------

  private static int helper_tail_recursion(int start, int [] ls, int acc) {
    if (start >= ls.length) {
      return acc;
    }
    // this is a tail recursion because the final instruction is the recursive call.
    return helper_tail_recursion(start+1, ls, acc+ls[start]);
  }
    
  public static int sum_tail_recursion(int [] ls) {
    if (ls == null || ls.length == 0) {
      return 0;
    }
    return helper_tail_recursion(0, ls, 0);
  }
}
```

The principle behind eliminating the recursion stack for tail recursion:
the compiler knows that after returning from the callee, the function will immediately return again, so it does not need the data saved in the function call stack. Only one stack frame is needed, and all levels share that stack frame, so the return process can skip the entire recursion stack.

Not all language compilers support tail call optimization. For example, C and C++ support it, while Python and Java do not.

Tail recursion is usually not that easy to implement. It requires the recursive call to appear only as the final instruction. If multiple functions need to be called, or if the return value needs further computation, then it cannot be tail recursion.

Careful readers may notice the similarity between tail recursion and iteration (loops). In fact, some functional programming languages do not even support loops; they only have recursion, yet can fully implement iteration. Since we usually use loops more often and tail recursion less often, if you need to write tail recursion, usually because an interview asks for it, you can first write the loop version. Then try to turn the updates of local variables into parameters of the tail-recursive function. This often leads to elegant tail-recursive code, though for most people its readability is not high.

## Conclusion

The pattern for solving recursion problems:
1. Define the recursive function
2. Write down the recurrence relation and base case
3. Use memoization to eliminate repeated computation if it exists
4. If possible, implement the recursive algorithm with tail recursion to reduce space complexity
