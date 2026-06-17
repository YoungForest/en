---
title: LeetCode Weekly Contest 239
date: 2021-05-02 11:47:52
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 78 / 10870 | YoungForest | 18 | 0:52:57 | 0:02:22 | 0:09:37 | 0:27:13 | 0:47:57  1 |

I have been exempt from check-ins for three consecutive weeks, and last night's biweekly contest also went pretty well as a hand-speed contest.

The recent weekly contests have indeed become somewhat easier. It seems I am still more suited to easy problems. Hard+ problems are still not quite my thing.

<!-- more -->

## 1848. Minimum Distance to the Target Element

A warm-up problem.

One pass, recording the best answer.

```cpp
class Solution {
    const int INF = 0x3f3f3f3f;
public:
    int getMinDistance(vector<int>& nums, int target, int start) {
        int ans = -INF;
        for (int i = 0; i < nums.size(); ++i) {
            if (nums[i] == target) {
                if (abs(i - start) < abs(ans - start)) {
                    ans = i;
                }
            }
        }
        return abs(ans - start);
    }
};
```

## 1849. Splitting a String Into Descending Consecutive Values

Backtracking, searching possible split points.

```python
class Solution:
    def splitString(self, s: str) -> bool:
        n = len(s)
        if n <= 1: return False
        @cache
        def dfs(i: int, last: int) -> bool:
            if i >= n: return False
            if i != 0:
                final = int(s[i:])
                if final < last and last - final == 1:
                    return True
            for j in range(i + 1, n):
                new = int(s[i:j])
                if new < last and (i == 0 or last - new == 1):
                    if dfs(j, new): return True
            return False
        return dfs(0, float('inf'))
```

Time complexity: O(N^2),
space complexity: O(N).

Although we used backtracking, which looks like it should have high time complexity, O(N ^ N), in fact in the `for` loop inside `dfs`, there is only one possible path that satisfies the condition and can `dfs` to the next layer.

Refer to [this solution's analysis](https://leetcode.com/problems/splitting-a-string-into-descending-consecutive-values/discuss/1186795/C%2B%2B-Backtracking-solution.-O(N-2)-and-time-complexity-analytics).

## 1850. Minimum Adjacent Swaps to Reach the Kth Smallest Number

First, we need to review [LC 31. Next Permutation](https://leetcode.com/problems/next-permutation/) and learn how to solve the next enumerated permutation. Fortunately, C++ STL already provides the `std::next_permutation` function, which can be used directly. Its amortized time complexity is **O(1)**.

Use `next_permutation` `k` times to find the Kth Smallest Number.

After that, just use a greedy idea to find the minimum number of swaps.

That is, whenever a position differs, find the nearest equal digit behind it and move it to the current position through swaps.

```cpp
class Solution {
public:
    int getMinSwaps(string num, int k) {
        auto s = num;
        while(std::next_permutation(s.begin(), s.end()) && k - 1 > 0) {
            // std::cout << s << '\n';
            --k;
        }
        const int n = num.size();
        int ans = 0;
        for (int j = 0; j < n; ++j) {
            if (num[j] == s[j]) continue;
            // find next digit and swap it here
            int i = j + 1;
            for (; i < n and num[i] != s[j]; ++i);
            // cout << i << " " << j << endl;
            ans += i - j;
            for (int x = i; x > j; --x) {
                swap(num[x], num[x - 1]);
            }
        }
        return ans;
    }
};
```

Time complexity: O(k + n^2),
space complexity: O(n).

## 1851. Minimum Interval to Include Each Query

This problem is somewhat similar to [the last problem from last night's biweekly contest](https://youngforest.github.io/en/2021/05/02/LeetCode-biweekly-contest-51/#1847-Closest-Room), and it needs the offline query technique.

Sort intervals by size from small to large. Whenever a new interval is added, update the answers for the queries it can cover.

Here we need to use `multimap` to maintain unresolved `queries`, because it supports binary search, deletion, and duplicate queries.

```cpp
class Solution {
public:
    vector<int> minInterval(vector<vector<int>>& intervals, vector<int>& queries) {
        // brute-foce: queries.length * intervals.length
        // smarter: queries.length * log + intervals.length * log
        const int n = intervals.size(), m = queries.size();
        // sort interval by size less<>, find new queries
        vector<int> ans(m, -1);
        multimap<int, int> indexQueries;
        for (int i = 0; i < m; ++i) {
            indexQueries.insert({queries[i], i});
        }
        sort(intervals.begin(), intervals.end(), [](const auto& lhs, const auto& rhs) -> bool {
            return lhs[1] - lhs[0] < rhs[1] - rhs[0];
        });
        int size = 0;
        for (const auto& v : intervals) {
            size = v[1] - v[0] + 1;
            auto left = indexQueries.lower_bound(v[0]);
            auto right = indexQueries.upper_bound(v[1]);
            for (auto it = left; it != right; ++it) {
                ans[it->second] = size;
            }
            for (auto it = left; it != right;) {
                auto nit = next(it);
                indexQueries.erase(it);
                it = nit;
            }
        }
        return ans;
    }
};
```

n = intervals.length, m = queries.length

Time complexity: O(n log n + m log m + n log m),
space complexity: O(m).
