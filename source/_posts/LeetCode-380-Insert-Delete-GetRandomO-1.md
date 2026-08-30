---
title: 'LeetCode #380 Insert Delete GetRandom O(1)'
date: 2018-10-26 23:19:20
tags:
- LeetCode
categories:
description: 'Designing a randomized set with hash-based lookup, swap-with-last deletion and average constant-time operations.'
translations:
  zh-CN: https://youngforest.github.io/2018/10/26/LeetCode-380-Insert-Delete-GetRandomO-1/
  en: https://youngforest.github.io/en/2018/10/26/LeetCode-380-Insert-Delete-GetRandomO-1/
---
<figure class="editorial-illustration editorial-illustration--hero"><img src="/en/images/ai/LeetCode-380-Insert-Delete-GetRandomO-1/en-hero.webp" width="1536" height="864" alt="Forest uses index cords to find storage slots, fills a deletion gap with the last crate and samples through an even spinner" decoding="async" fetchpriority="high"></figure>

<!-- more -->

The key point of this problem is understanding "average O(1) time", which is also an important concept in time-complexity analysis: "amortized".
In *Algorithms, Fourth Edition*, the analysis of many data-structure operations uses this method. So "amortized time complexity" is often associated with operations on the corresponding data structure. When I interviewed at Megvii in May, the second problem asked me to construct a queue data structure that maintains the maximum value, and the final requirement was that the operation time complexity be "amortized O(1)". Unfortunately, at that time I was not familiar with the concept of "amortized". I could analyze worst-case time complexity, and although I eventually derived the correct answer under the interviewer's guidance, the final result was predictably no hire.

Description: https://leetcode.com/problems/insert-delete-getrandom-o1/description/
Solution: None
Difficulty: Medium

[answer](https://leetcode.com/problems/insert-delete-getrandom-o1/discuss/85401/Java-solution-using-a-HashMap-and-an-ArrayList-along-with-a-follow-up.-(131-ms))

The key point is to use a hashmap for O(1) lookup, while ArrayList conveniently supports random access by index.

```python
class RandomizedSet:

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.array = []
        self.index_map = {}
        

    def insert(self, val):
        """
        Inserts a value to the set. Returns true if the set did not already contain the specified element.
        :type val: int
        :rtype: bool
        """
        if val in self.index_map:
            return False
        
        self.index_map[val] = len(self.array)
        self.array.append(val)
        
        return True
        

    def remove(self, val):
        """
        Removes a value from the set. Returns true if the set contained the specified element.
        :type val: int
        :rtype: bool
        """
        if val not in self.index_map:
            return False
        
        self.array[self.index_map[val]] = self.array[-1]
        self.index_map[self.array[-1]] = self.index_map[val]
        self.array.pop()
        self.index_map.pop(val)
        
        return True
        

    def getRandom(self):
        """
        Get a random element from the set.
        :rtype: int
        """
        rnd = random.randint(0, len(self.array)-1)
        
        return self.array[rnd]
        


# Your RandomizedSet object will be instantiated and called as such:
# obj = RandomizedSet()
# param_1 = obj.insert(val)
# param_2 = obj.remove(val)
# param_3 = obj.getRandom()
```
