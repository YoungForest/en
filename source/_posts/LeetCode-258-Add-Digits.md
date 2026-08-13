---
title: LeetCode 258 Add Digits
date: 2019-04-06 17:08:06
tags:
- LeetCode
categories:
- Programming
translations:
  zh-CN: https://youngforest.github.io/2019/04/06/LeetCode-258-Add-Digits/
  en: https://youngforest.github.io/en/2019/04/06/LeetCode-258-Add-Digits/
---
While grinding problems today, I encountered an interesting one: given a number, repeatedly add the digits until the sum is less than 10. [Link](https://leetcode.com/problems/add-digits/description/).
The problem itself is not hard; recursion or iteration can both solve it. But solving it in O(1) complexity is the real point.

The answer is simple: 1 + (num - 1) % 9.
If you are interested, you can read the proof and extension here: [Wikipedia](https://en.wikipedia.org/wiki/Digital_root#Congruence_formula).
