---
title: Alibaba DingTalk Server Development Summer Intern Interview
date: 2020-03-04 19:34:08
tags:
- Alibaba
- intern
categories:
- interview
description: A DingTalk backend internship interview spanning linked lists, two-sum, C++, compilers, operating systems, networking, and career plans.
translations:
  zh-CN: https://youngforest.github.io/2020/03/04/Alibaba-Dingding-backend-intern-interview/
  en: https://youngforest.github.io/en/2020/03/04/Alibaba-Dingding-backend-intern-interview/
---
The interview was done by DingTalk phone/video call, and the hand-written coding part was completed through Alibaba's online platform.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/Alibaba-Dingding-backend-intern-interview/en-hero.webp" alt="Forest sits at an unmarked remote interview table with linked carriages, paired weights, and a widening row of engineering tools" width="1536" height="864" decoding="async">
</figure>

<!-- more -->

There were two algorithm questions:

1. Implement the data structure for a doubly linked list.

2. `twoSum`: find the indices of two numbers in an array whose sum equals `target`.

The difficulty was around LeetCode Easy. But during the interview, I needed to ask questions and communicate with the interviewer to clarify the problem. The interviewer also cared a lot about code cleanliness and efficiency, such as parameter validation and assumptions in the implementation.

```cpp
//评测题目: 实现一个简单的双向链表，要求完成 node和list的构造函数，以及 list类的void push_back(node*), void remove(node*) 方法

class node{
public:
	node* prev;
	node* next;

public:
    node() {
      prev = nullptr;
      next = nullptr;
    }
};

class list{
private:
	node* head;
	node* tail;
public:
	list() {
      head = nullptr;
      tail = nullptr;
    }
	
	void push_back(node* n) {
      // 假设n不在list中
    	if (n) {
          if (tail) {
          	tail->next = n;
          } else {
            head = n;
          }
          n->prev = tail;
          tail = n;
        }
    }
	void remove(node* n) {
      if (n == nullptr) return;
      auto current = head;
      while (current) {
        if (current == n) {
          // find
          if (current->prev && current->next) {
            auto p = current->prev;
            auto n = current->next;
            p->next = n;
            n->prev = p;
          } else if (current->prev) {
            tail = current->prev;
            tail->next = nullptr;
            current->prev = nullptr;
          } else if (current->next) {
            head = current->next;
            head->prev = nullptr;
            current->next = nullptr;
          } else {
            head = nullptr;
            tail = nullptr;
          }
          break;
        }
        current = current->next;
      }
      
    }
};

//给定一个vecotr<int>v和一个目标target，找到v中两个数字的和等于target，返回数字在原数组中的下标（多个解的返回任意一个即可），无解返回空vector。
//Example:  v=[7,11,2,15],target=9,
//因为nums[0]+nums[2]=7+2=9, 
//return[0,2].
vector<int> helper(vector<int>& v,int target) {
	vector<int> copy = v;
    sort(copy.begin(), copy.end());
    int left = 0, right = v.size() - 1;
    while (left < right) {
        int current_sum = copy[left] + copy[right];
    	if (current_sum == target) {
          return {copy[left], copy[right]};
        } else if (current_sum < target) {
          ++left;
        } else {
          --right;
        }
    }
    return {};
}
vector<int> twoSum(vector<int>& v,int target) {
    if (v.size() <= 1) return {};
	auto values = helper(v, target);
  	if (values.empty()) return {};
    vector<int> ans(2);
    int index =  0;
  	
      if (values[0] == values[1]) {
        for (int i = 0; i < v.size(); ++i) {
          if (v[i]  == values[0])  {
            ans[index] = i;
            ++index;
            if (index == 2)
              break;
          }
        }
      } else {
        for (int i = 0; i < v.size(); ++i) {
          if (v[i] == values[0]) {
            ans[0] = i;
          } else if (v[i] == values[1]) {
            ans[1] = i;
          }
        }
      }
    }
  
    return ans;
}
```

<figure class="editorial-illustration">
  <img src="/en/images/ai/Alibaba-Dingding-backend-intern-interview/en-knowledge-gauntlet.webp" alt="Forest carries a toolbox through a corridor of compiler gears, expanding containers, locks, a memory maze, and a broken network bridge" width="1536" height="864" loading="lazy" decoding="async">
</figure>

My main programming language is C++, so the interviewer asked many C++-related questions.

From my resume, he looked at my [GitHub profile](https://github.com/YoungForest) and asked which project I wanted to introduce the most. I introduced the [compiler](https://github.com/YoungForest/C0Compiler) I implemented in my junior year. Then he asked about some compiler processes and data structures, such as lexical analysis, parsing, intermediate code generation, target code generation, optimization, symbol table, and DAG optimization.

In C++, how does the compiler distinguish `>>` in input from `>>` in nested templates? I answered that it should be distinguishable during parsing. Then we talked about some history around this: in the past, `>>` in templates had to be written as `> >` to compile.

Implementation of the C++ HashMap container, and how a hash value is mapped into buckets.

How `vector` expansion is implemented in STL.

The difference between `new` and `malloc`.

Move semantics and rvalue references.

The differences among `weak_ptr`, `shared_ptr`, and `unique_ptr`.

What I am usually interested in, especially advanced data structures:

Order tree and segment tree.

Implementation of quicksort and heapsort. Maintaining a min heap.

The difference between process and thread.

Types of locks and read-write control.

Linux memory management mechanism.

Common shell commands. File operations, process information (`top`, `ps`), and stack information (`lldb`).

TCP packet loss handling. I did not answer this well. I had forgotten a lot of the computer networking knowledge I learned in the second semester of junior year.

The 24-point puzzle: 4, 4, 10, 10, `(10 * 10 - 4) / 4`.

At first I thought it was a programming problem and answered "backtracking." Later I realized it was purely an intelligence puzzle.

Career planning.
