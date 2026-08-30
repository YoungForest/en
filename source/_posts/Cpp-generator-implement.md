---
title: Implementing Generator in C++ (Generator, yield in Python)
date: 2019-12-15 21:34:30
tags:
- C++
categories:
- Programming
description: A short note on replacing an inelegant precompute-and-store iterator with a generator that yields combinations on demand in C++.
translations:
  zh-CN: https://youngforest.github.io/2019/12/15/Cpp-generator-implement/
  en: https://youngforest.github.io/en/2019/12/15/Cpp-generator-implement/
---
This started from a LeetCode problem: [1286. Iterator for Combination](https://leetcode.com/problems/iterator-for-combination/). The most perfect implementation would use a Generator, namely Python's `yield`.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/Cpp-generator-implement/en-hero.webp" alt="A sprawling storeroom prepares every combination crate in advance while a compact lift releases one neatly assembled crate per pull" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

But I did not know how to do that, so I only implemented a solution that precomputes everything and stores the results. It was not elegant. After the contest, I learned an implementation of Generator in C++ and am sharing it here. I did not find a good Chinese article about this online.
