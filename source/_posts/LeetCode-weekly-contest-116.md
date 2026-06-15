---
title: LeetCode weekly contest 116
date: 2018-12-26 11:26:03
tags:
- Competitive Programming
categories:
- LeetCode
---

It is time for the weekend LeetCode weekly contest again. This time the result was not good. The main reason was that I wanted to solve both the second and third problems, and ended up solving neither. If I had focused all my time on the second problem, I probably could still have ACed it.

## 961. N-Repeated Element in Size 2N Array
This problem always felt like something I had already done on LeetCode before, and I still remembered the direction of the solution.
The idea is: since half of the elements are the same, randomly pick two elements and check whether they are equal. Probabilistically, there is a chance that it never finishes, but in practical use it works well.

```python
import random

class Solution:
    def repeatedNTimes(self, A):
        """
        :type A: List[int]
        :rtype: int
        """
        while True:
            s = random.sample(A, 2)
            if s[0] == s[1]:
                return s[0]
```

## 962. Maximum Width Ramp
Maybe I have been studying DP too much recently. Whenever I encounter a problem, I immediately think of trading space for time. But I forgot where DP is actually useful:
When should we look for a Dynamic Programming solution to a problem?
1. Optimal substructure
2. Overlapping subproblems

I searched for optimal substructure and overlapping subproblems for a long time:
`dp[x] = max[dp[x] + x - k] for all A[x] >= A[k]`,
and found that the time complexity was the same as brute search, both O(n^2).

I had really become obsessed with DP and forgotten other more basic algorithms. In fact, this problem can be solved with simple sorting.
Sorting avoids the double loop and reduces complexity from O(n^2) to O(n logn).

```python
class Solution:
    def maxWidthRamp(self, A):
        """
        :type A: List[int]
        :rtype: int
        """
        min_value = math.inf
        ret = 0
        for i in sorted(range(len(A)), key=A.__getitem__):
            min_value = min(min_value, i)
            ret = max(ret, i - min_value)
            
        return ret
```

The Solution also gives a binary-search approach, which I think is clever.
The key point is to notice that for `j1 < j2`, `A[j1] <= A[j2]`, we always prefer choosing `j2`. So for input like `[6,0,8,2,1,5]`, the `A[j]` of the maximum width ramp must appear in `[5, 8]`. For choosing `i`, we still need one traversal. Then which one in `[5, 8]` is `A[j]`? That requires binary search based on `A[i]`, choosing the `A[j]` just slightly larger than `A[i]`.

```python
import bisect

class Solution:
    def maxWidthRamp(self, A):
        """
        :type A: List[int]
        :rtype: int
        """
        condinates = [(A[-1], len(A)-1)]
        ret = 0
        
        for i in range(len(A)-2, -1, -1):
            jx = bisect.bisect(condinates, (A[i],))
            if jx >= len(condinates):
                condinates.append((A[i], i))
            else:
                ret = max(ret, condinates[jx][1] - i)
            
        return ret
```

## 963. Minimum Area Rectangle II

This problem is honestly unpleasant. No wonder downvotes are far greater than upvotes. It requires much more math than algorithms, and brute search, O(n^3), can also pass.

```python
import math
import collections
import itertools

class Solution:
    def minAreaFreeRect(self, points):
        """
        :type points: List[List[int]]
        :rtype: float
        """
        EPS = 1e-7
        points_set = set(map(lambda x: complex(*x), points))
        ans = math.inf
        
        for p1, p2, p3 in itertools.permutations(points_set, 3):
            p4 = p1 + p3 - p2
            if p4 in points_set and abs((p3 - p2).real * (p1 - p2).real + (p3 - p2).imag * (p1 - p2).imag) < EPS:
                ans = min(ans, abs(p1 - p2) * abs(p3 - p2))
                
        return ans if ans < math.inf else 0
```

The Solution also has an O(n^2 log n) approach that uses the property of a rectangle's center.

## 964. Least Operators to Express Number

Last week's weekly contest dragged on until this Saturday before I finished it, and tomorrow is already the next weekly contest. My efficiency is truly touching. But near the end of the year, there are indeed a lot of things going on. Still, no matter how busy I am, I cannot forget the central policy of "finding a good job".

This problem is Hard, and as expected, I had no idea how to solve it. Read the solution first and learn.
The official solution was a mess for me; I had no idea what it was saying. Fortunately, I found a very understandable [Discuss post](https://leetcode.com/problems/least-operators-to-express-number/discuss/208376/python2-O(log-target)-chinese).
As the author says:
> Fundamentally, this problem tests understanding of numeral systems.

Time complexity: O(log target),
space complexity: O(log target).

```python
class Solution:
    def leastOpsExpressTarget(self, x, target):
        """
        :type x: int
        :type target: int
        :rtype: int
        """
        rl = []
        while target > 0:
            rl.append(target % x)
            target //= x
            
        n = len(rl)
            
        pos = rl[0] * 2
        neg = (x - rl[0]) * 2
        
        for i in range(1, n):
            pos, neg = min(rl[i] * i + pos, rl[i] * i + i + neg), min((x - rl[i]) * i + pos, (x - rl[i]) * i - i + neg)
            
        return min(pos - 1, n + neg - 1)
```
