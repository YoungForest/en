---
title: LeetCode Weekly Contest 236
date: 2021-04-11 16:00:09
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 1513 / 12115 | YoungForest | 12 | 0:45:18 | 0:02:57 | 0:08:59 | 0:40:18  1 | null |

## 1822. Sign of the Product of an Array

A warm-up problem. Count how many negative numbers there are and whether there is a 0.

```python
class Solution:
    def arraySign(self, nums: List[int]) -> int:
        x = 1
        for i in nums:
            x *= i
        if x > 0:
            return 1
        elif x == 0:
            return 0
        else:
            return -1
```

Time complexity: O(N),
space complexity: O(1).

## 1823. Find the Winner of the Circular Game

The classic Josephus problem. I casually Googled one: [Josephus Ring - Formula Method](https://blog.csdn.net/u011500062/article/details/72855826).

```cpp
class Solution {
    int cir(int n,int m)
    {
        int p=0;
        for(int i=2;i<=n;i++)
        {
            p=(p+m)%i;
        }
        return p+1;
    }
public:
    int findTheWinner(int n, int k) {
        return cir(n, k);
    }
};
```

Time complexity: O(N),
space complexity: O(1).

## 1824. Minimum Sideway Jumps

Dynamic Programming.

`dp[i][j]` means the minimum number of side jumps needed from position `i`, lane `j`, to the end.

One thing to note: in this problem `N <= 5 * 10^5`, so Python top-down DP will blow the stack.

Therefore, during the contest I had one Runtime Error. Adding `sys.setrecursionlimit(110000)` still did not work, so I changed it to bottom-up DP.

```python
class Solution:
    def minSideJumps(self, obstacles: List[int]) -> int:
        n = len(obstacles) - 1
        dp = [[float('inf')] * 4 for i in range(n + 1)]
        dp[n][1] = dp[n][2] = dp[n][3] = 0
        for i in range(n - 1, -1, -1):
            for j in (1, 2, 3):
                if obstacles[i+1] != j:
                    dp[i][j] = min(dp[i][j], dp[i+1][j])
                for nj in (1, 2, 3):
                    if j == nj: continue
                    if obstacles[i] != nj and obstacles[i+1] != nj:
                        dp[i][j] = min(dp[i][j], dp[i+1][nj] + 1)
        return dp[0][2]
```

Time complexity: O(N),
space complexity: O(1).

## 1825. Finding MK Average


