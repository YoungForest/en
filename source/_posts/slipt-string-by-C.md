---
title: Splitting Strings in C - YoungForest
tags: []
date: 2016-10-11 22:11:00
description: A short note on using strtok to split strings in C, including the key detail for calls after the first.
translations:
  zh-CN: https://youngforest.github.io/2016/10/11/slipt-string-by-C/
  en: https://youngforest.github.io/en/2016/10/11/slipt-string-by-C/
---

<figure class="editorial-illustration editorial-illustration--hero"><img src="/en/images/ai/slipt-string-by-C/en-hero.webp" width="1536" height="864" alt="A continuous bead ribbon enters a splitter once, then an empty handoff lever releases the remembered remainder in tidy segments" decoding="async" fetchpriority="high"></figure>

<!-- more -->
**[Abstract]** Recently, while working on a C language problem, I needed to split a string. I originally wanted to write it by hand, which would not be too troublesome, but then I thought: other languages all have library functions for splitting strings, so how could C not have one? I searched online, and sure enough, such a function exists and is quite useful. I summarize it here. In the C language manual, it is easy to find the usage of `strtok`. One thing to note is that, except for the first call, the first argument should be passed... [Read more](http://www.cnblogs.com/YoungForest/p/5950994.html)
