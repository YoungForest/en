---
title: LeetCode Biweekly Contest 37
date: 2020-10-18 09:54:27
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/10/18/LeetCode-biweekly-contest-37/
  en: https://youngforest.github.io/en/2020/10/18/LeetCode-biweekly-contest-37/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 893 / 8250 | YoungForest | 7 | 0:27:31 | 0:11:24 | 0:27:31 |  null | null |

I have crashed in two consecutive biweekly contests.
The third problem passed two minutes after the contest ended. It was originally within my ability, but I got too anxious near the end. My state was not good at night in the first place, and ironically I wrote it right after the contest was over.

## 1619. Mean of Array After Removing Some Elements

A warm-up problem. Sort first, then sum, then take the average.
One thing to note is that the constraints guarantee `arr.size() % 20 == 0`.


```cpp
class Solution {
public:
    double trimMean(vector<int>& arr) {
        sort(arr.begin(), arr.end());
        const int n = arr.size();
        const int x = n / 20;
        double s = 0.0;
        for (int i = 0; i < n; ++i) {
            if (i < x || (n - 1 - i) < x) continue;
            s += arr[i];
        }
        return s / (n - 2 * x);
    }
};
```
Time complexity: O(N log N),
space complexity: O(1).

## 1620. Coordinate With Maximum Network Quality

The constraints are small, only 50, so direct brute force works. Enumerate every position, then enumerate every tower to calculate its signal.

```cpp
class Solution {
public:
    vector<int> bestCoordinate(vector<vector<int>>& towers, int radius) {
        // 50 * 50 * 50
        const double r = radius;
        int maxSignal = 0;
        int maxI = 0;
        int maxJ = 0;
        auto distance = [&](const int a1, const int b1, const int a2, const int b2) -> double {
            return  sqrt((a1 - a2) * (a1 - a2) + (b1 - b2) * (b1 - b2));
        };
        auto getSignal = [&](const int i, const int j) -> int {
            int ans = 0;
            for (const auto& t : towers) {
                double d = distance(t[0], t[1], i, j);
                if (d > r) continue;
                ans += static_cast<int>(floor(t[2] / (1 + d)));
            }
            return ans;
        };
        for (int i = 0; i <= 50; ++i) {
            for (int j = 0; j <= 50; ++j) {
                const int signal = getSignal(i, j);
                // cout << i << " " << j << " " << signal << endl;
                if (signal > maxSignal) {
                    maxI = i;
                    maxJ = j;
                    maxSignal = signal;
                }
            }
        }
        return {maxI, maxJ};
    }
};
```

Time complexity: O(rows * cols * towers),
space complexity: O(1).

## 1621. Number of Sets of K Non-Overlapping Line Segments

Dynamic Programming. I could immediately tell it was a DP problem, but still took a detour. At first I wrote an `O(n^2 * k)` algorithm and did not make full use of the subproblems.
In fact, adding one more variable to indicate whether the beginning must be a segment makes it much easier to write.

```cpp
class Solution:
    def numberOfSets(self, m: int, kk: int) -> int:
        MOD = int(10**9 + 7)
        @lru_cache(None)
        def dp(n, k, first):
            if n < k:
                return 0
            if n == k:
                return 1
            if k == 1:
                return ((n + 1) * n) // 2
            if first:
                return (dp(n - 1, k, True) + dp(n - 1, k - 1, False)) % MOD;
            else:
                return (dp(n - 1, k, False) + dp(n, k, True)) % MOD
        return dp(m - 1, kk, False)
```

Time complexity: O(n * k),
space complexity: O(n * k).

## 1622. Fancy Sequence

This problem is already beyond my current ability. I had no idea at all.
After studying the discussion section once, it seems there are roughly two types of algorithms:
- [modular multiplicative inverse](https://leetcode-cn.com/problems/fancy-sequence/solution/qi-miao-xu-lie-by-zerotrac2/)
- [lazy segment tree](https://leetcode-cn.com/problems/fancy-sequence/solution/ru-guo-bu-liao-jie-cheng-fa-ni-yuan-jiu-hao-hao-xu/)

