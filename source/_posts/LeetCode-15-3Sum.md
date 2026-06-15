---
title: 'LeetCode #15 3Sum'
date: 2018-10-25 23:54:23
tags:
- LeetCode
categories:
---

I happened to encounter this classic problem during my interview at JingChi. It was asked by Eric in the second round. I had not done this problem before, but I had done the related 2Sum problem, after all it is the first LeetCode problem and probably many people have done it. Also, when *Algorithms, Fourth Edition* discusses algorithm complexity, it uses the same kind of problem, though the details may differ, such as requiring no duplicate triplets in the result. I still had some impression of it at the time. I smoothly wrote an O(n^2) time-complexity solution, although afterward I found small bugs, such as list `sort` being in-place. But it did not really matter.
Today I organized the solution from the interview, submitted it, and unexpectedly got Time Limit Exceeded.

Description: https://leetcode.com/problems/3sum/description/
Solution: None
Difficulty: Medium

## Solution During the Interview

```python
class Solution:
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        mymap = {}
        results = set()
        for i in range(len(nums)):
            for j in range(i + 1, len(nums)):
                if - nums[i] - nums[j] in mymap:
                    a = [- nums[i] - nums[j], nums[i], nums[j]]
                    a.sort()
                    results.add(tuple(a))
            mymap[nums[i]] = i
        
        re = []
        for a in results:
            re.append(list(a))
        return re
```

## Solving the Timeout Problem

reference[https://fizzbuzzed.com/top-interview-questions-1/]

The overall idea is that O(n^2) time complexity cannot be lowered further. The work is more about trying small techniques to reduce the constants in the time complexity.

### Sort First

```python
class Solution:
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        nums.sort()
        mymap = set()
        results = set()
        for i in range(len(nums)):
            if i > 1 and nums[i] == nums[i-2]:
                continue
            for j in range(i + 1, len(nums)):
                if j != i + 1 and nums[j] == nums[j-1]:
                    continue
                if - nums[i] - nums[j] in mymap:
                    a = [- nums[i] - nums[j], nums[i], nums[j]]
                    a.sort()
                    results.add(tuple(a))
            mymap.add(nums[i])
        
        re = []
        for a in results:
            re.append(list(a))
        return re
```

Adding sorting and checks inside the two loops reduces the number of duplicate triplets, meaning `result.add(tuple(a))` is executed fewer times.
Looking at the previous TLE test case, LeetCode was clearly targeting duplicate triplets. Python `set.add` has O(1) time complexity, but it still got blocked by constants.


### Two Pointer, Space Complexity Reduced to O(1)

```python
class Solution:
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        nums.sort()
        results = []
        
        for i in range(len(nums)):
            # never let i refer to the same value twice
            if i != 0 and nums[i] == nums[i-1]:
                continue
                
            j = i + 1
            k = len(nums) - 1
            
            while j < k:
                if nums[i] + nums[j] + nums[k] == 0:
                    results.append([nums[i], nums[j], nums[k]])
                    j += 1
                    k -= 1
                    while j < k and nums[j] == nums[j-1]: # never let j refer the same value twice
                        j += 1
                elif nums[i] + nums[j] + nums[k] < 0:
                    j += 1
                else:
                    k -= 1
        
        return results
```

The first submission still timed out, but without changing anything, I submitted again and it was Accepted. It seems LeetCode also depends on luck.
