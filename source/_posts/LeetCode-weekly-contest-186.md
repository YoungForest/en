---
title: LeetCode Weekly Contest 186
date: 2020-04-26 13:04:58
description: A weekly review of split scores, cards taken from both ends, diagonal traversal and constrained-subset DP, plus the tradeoffs of a faster language.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/04/26/LeetCode-weekly-contest-186/
  en: https://youngforest.github.io/en/2020/04/26/LeetCode-weekly-contest-186/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-186/en-hero.webp" alt="Forest races past balanced bead halves, cards taken from both ends, diagonal cube trays and a descending block queue" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (4) |	Q3 (6) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 113 / 11684 |	YoungForest | 18 | 0:34:17 | 0:04:32 |  0:10:54 | 0:18:49 | 0:34:17 |

A speed contest. After switching to Python, speed contests became much easier. Life is short; I use Python. But while using Python for problem solving, I also discovered some issues.
- I am not familiar enough with the API. For example, `sorted` returns a sorted `list`, rather than doing an in-place sort.
- The data structures are incomplete. For example, there is no TreeMap, and `priority_queue` can only be replaced with `heap`. For a max heap, all numbers need to be multiplied by `-1`, which is an ugly implementation.
- runtime error. With some typos, there can be wrong submissions. At this point, your test cases become very important: whether they cover all cases and boundary conditions.

## 1422. Maximum Score After Splitting a String

Brute force. `2 <= s.length <= 500`.

Time complexity: O(N ^ 2),
space complexity: O(1).

Also, if using a prefix sum approach, the time complexity can be reduced to `O(N)`.

```python
class Solution:
    def maxScore(self, s: str) -> int:
        def count(s, c):
            ans = 0
            for i in s:
                if i == c:
                    ans += 1
            return ans
        return max(count(s[:x], '0') + count(s[x:], '1') for x in range(1,len(s)))
```

## 1423. Maximum Points You Can Obtain from Cards

The problem can be transformed into: take `x` cards from the head and `k-x` cards from the tail, and maximize the sum.

Time complexity: O(N),
space complexity: O(N).

```python
class Solution:
    def maxScore(self, cardPoints: List[int], k: int) -> int:
        n = len(cardPoints)
        presum = [0] * (n + 1)
        suffixsum = [0] * (n + 1)
        for i in range(1, n+1):
            presum[i] = presum[i-1] + cardPoints[i-1]
            suffixsum[i] = suffixsum[i-1] + cardPoints[n-i]
        
        return max(presum[x] + suffixsum[k-x] for x in range(k+1)) # take x from head, k - x from tail
```

There is also a mainstream method in the discussion section: transform the problem into finding the subarray of size `num.size()-k` with the minimum sum. Use a sliding window, with time complexity O(N) and space complexity O(1).

## 1424. Diagonal Traverse II

Traverse once and put elements onto the proper diagonals. Use a `dict` to store diagonals, with each diagonal being a `list`.
One thing to note is that in Python, `dict` is a HashMap, and after 3.7 it guarantees that traversal order is consistent with insertion order. So here we can directly traverse the diagonal dictionary.

Time complexity: O(the number of elements),
space complexity: O(the number of elements).

```python
class Solution:
    def findDiagonalOrder(self, nums: List[List[int]]) -> List[int]:
        line = defaultdict(list)
        for start in range(len(nums)):
            for i in range(len(nums[start])):
                line[start + i].append(nums[start][i])
        ans = []
        for i in line:
            while line[i]:
                ans.append(line[i].pop())
        return ans
```

## 1425. Constrained Subset Sum

Dynamic Programming. `dp[i]` represents the maximum subset sum ending at the `i`-th element.
`dp[i] = max(dp[j] + nums[i] for i - j >= k)`.
By observation, when a non-negative number is encountered, we can break early, because the subset should definitely include positive numbers as much as possible.

Time complexity: O(n * k),
space complexity: O(n).

```python
class Solution:
    def constrainedSubsetSum(self, nums: List[int], k: int) -> int:
        n = len(nums)
        dp = nums[:]
        ans = float('-inf')
        for i in range(n):
            for j in range(k):
                index = i - 1 - j
                if index < 0:
                    break
                dp[i] = max(dp[i], dp[index] + nums[i])
                if nums[index] >= 0:
                    break
            ans = max(ans, dp[i])
        return ans
```

There is a correct O(N) solution in the [discussion section](https://leetcode.com/problems/constrained-subset-sum/discuss/597693/PythonC%2B%2B-DP-with-decreasing-deque). It uses a monotonic decreasing deque, which can maintain `max(dp[j] + nums[i] for i - j >= k)` in O(1) time.
