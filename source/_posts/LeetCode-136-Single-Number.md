---
title: 'LeetCode #136 Single Number'
date: 2018-09-12 15:38:08
tags:
categories:
- LeetCode
description: Finding the single number under linear-time and constant-space constraints through pairwise XOR cancellation.
translations:
  zh-CN: https://youngforest.github.io/2018/09/12/LeetCode-136-Single-Number/
  en: https://youngforest.github.io/en/2018/09/12/LeetCode-136-Single-Number/
---
<figure class="editorial-illustration editorial-illustration--hero"><img src="/en/images/ai/LeetCode-136-Single-Number/en-hero.webp" width="1536" height="864" alt="Forest passes matching stones through a cancellation arch while one accumulator bowl preserves the lone amber stone" decoding="async" fetchpriority="high"></figure>

<!-- more -->

Description: https://leetcode.com/problems/single-number/description/
Solution: https://leetcode.com/problems/single-number/solution/
Difficulty: Easy

The difficulty of the problem lies in this requirement: Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

I thought hard for a long time but still could not satisfy both O(n) time complexity and O(1) space complexity. I went to read the solution, and Approach 4 meets the requirement. It uses the properties of the XOR bit operation, which really is a bit of a trick. You can also see the comment section full of exclamations like "awesome." Once you know it, it is not hard; the next time I encounter it, it will be Easy.

```python
class Solution:
    def singleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        ret = 0
        for i in nums:
            ret ^= i
            
        return ret
```
