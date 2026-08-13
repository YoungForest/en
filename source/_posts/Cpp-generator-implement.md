---
title: Implementing Generator in C++ (Generator, yield in Python)
date: 2019-12-15 21:34:30
tags:
- C++
categories:
- Programming
translations:
  zh-CN: https://youngforest.github.io/2019/12/15/Cpp-generator-implement/
  en: https://youngforest.github.io/en/2019/12/15/Cpp-generator-implement/
---
This started from a LeetCode problem: [1286. Iterator for Combination](https://leetcode.com/problems/iterator-for-combination/). The most perfect implementation would use a Generator, namely Python's `yield`. But I did not know how to do that, so I only implemented a solution that precomputes everything and stores the results. It was not elegant. After the contest, I learned an implementation of Generator in C++ and am sharing it here. I did not find a good Chinese article about this online.
