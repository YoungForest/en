---
title: 'LeetCode #215 Kth Largest Element in an Array'
date: 2018-09-15 11:20:06
tags:
categories:
- LeetCode
---

Description: https://leetcode.com/problems/kth-largest-element-in-an-array/description/
Solution: https://leetcode.com/problems/kth-largest-element-in-an-array/discuss/
Difficulty: Medium

This was a problem my senior schoolmate was asked in his interview with JingChi. His interview was in the morning, and mine was in the afternoon. So after talking with him about the interview content, I solved all the problems he had been asked, including this one and [Coin Change](https://leetcode.com/problems/coin-change/description/).

## My Dynamic Programming

```python
class Solution:
    def findKthLargest(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        memo = {}
        mi = nums[0]
        ma = nums[0]
        for i in range(len(nums)):
            ma = max(ma, nums[i])
            mi = min(mi, nums[i])
            memo[(i, i+1)] = mi
            memo[(i, 1)] = ma
            
        def find(n, k):
            if (n, k) in memo:
                return memo[(n, k)]
            
            n_sub_1_k = find(n-1, k)
            
            if nums[n] <= n_sub_1_k:
                return n_sub_1_k
            
            n_sub_1_k_sub_1 = find(n-1, k-1)
            
            if nums[n] >= n_sub_1_k_sub_1:
                return n_sub_1_k_sub_1
            else:
                return nums[n]
            
        return find(len(nums)-1, k)
```

Time complexity is O(n^2), because every element in `memo` has to be traversed once.
Space complexity is O(n^2), because a two-dimensional `memo` is created.

Result: TLE.
This is worse than directly using quicksort-style partitioning to find the kth value.

## [Selection Algorithm](https://en.wikipedia.org/wiki/Selection_algorithm)

```python
class Solution:
    def findKthLargest(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        def partition(nums, lo, hi):
            i = lo + 1
            j = hi
            
            while True:
                while i <= hi and nums[i] < nums[lo]:
                    i += 1
                while j > lo and nums[j] > nums[lo]:
                    j -= 1
                
                if i >= j:
                    break
                    
                nums[i], nums[j] = nums[j], nums[i]
                i += 1
                j -= 1
                
            nums[lo], nums[j] = nums[j], nums[lo]
            
            return j
        
        lo = 0
        hi = len(nums) - 1
        k = len(nums) - k
        
        while lo < hi:
            j = partition(nums, lo, hi)
            if j < k:
                lo = j + 1
            elif j > k:
                hi = j - 1
            else:
                break
            
        return nums[k]
```

It is very similar to quicksort, especially the `partition` part.
Time complexity is O(n), and space complexity is O(n).
See the [Discuss post](https://leetcode.com/problems/kth-largest-element-in-an-array/discuss/60294/Solution-explained) for a detailed explanation.
