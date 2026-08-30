---
title: LeetCode biweekly contest 27
date: 2020-05-31 17:41:49
description: A speed-contest review of array reversal as sorting, fixed-window binary patterns, memoized prerequisite DFS and a two-robot cherry-grid DP.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/05/31/LeetCode-biweekly-contest-27/
  en: https://youngforest.github.io/en/2020/05/31/LeetCode-biweekly-contest-27/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-biweekly-contest-27/en-hero.webp" alt="Two shuffled tile rows settle into one order, two-tone bead windows fill pattern trays, remembered course paths are reused and twin robots cross a cherry grid" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 231 / 7926 |	YoungForest | 18 | 	0:42:16 | 0:04:51 |   0:10:55 | 0:22:31  1 |  0:37:16 |

A speed contest of decent quality. Some problems are worth thinking about: only after discovering the essence can you solve them quickly.

## 1460. Make Two Arrays Equal by Reversing Sub-arrays

Because there is no limit on the number of `reverse` operations, we can construct a strategy to transform two arrays into the same array. Similar to selection sort, each `reverse` can put one position in order. So the problem becomes: after sorting the two arrays, are they equal?
In C++, `vector`'s `==` does exactly this.

Time complexity: O(N log N),
Space complexity: O(1).

```cpp
class Solution {
public:
    bool canBeEqual(vector<int>& target, vector<int>& arr) {
        sort(target.begin(), target.end());
        sort(arr.begin(), arr.end());
        return target == arr;
    }
};
```

## 1461. Check If a String Contains All Binary Codes of Size K

Sliding window. The window size is K. Check whether every binary value in `0 - 2^K - 1` appears.

Time complexity: O(s.size()),
Space complexity: O(1 << K).

```cpp
class Solution {
public:
    bool hasAllCodes(string s, int k) {
        vector<bool> seen(1 << k, false);
        int current = 0;
        for (int i = 0; i < k && i < s.size(); ++i) {
            current = current * 2 - '0' + s[i];
        }
        seen[current] = true;
        for (int i = k; i < s.size(); ++i) {
            current = (current - (s[i-k]-'0') * (1 << (k-1))) * 2 - '0' + s[i];
            seen[current] = true;
        }
        return all_of(seen.begin(), seen.end(), [](const auto& a) -> bool {
            return a;
        });
    }
};
```

## 1462. Course Schedule IV

DFS. One thing to notice here is that `queries` itself is very large, but `n` is small. So we need memoized DFS to avoid repeated computation.

Time complexity: O(n ^ 3 + queries),
Space complexity: O(n ^ 2).

```cpp
class Solution {
public:
    vector<bool> checkIfPrerequisite(int n, vector<vector<int>>& prerequisites, vector<vector<int>>& queries) {
        unordered_map<int, vector<int>> edges;
        for (const auto& v : prerequisites) {
            edges[v[1]].push_back(v[0]);
        }
        map<pair<int,int>, bool> memo;
        function<bool(int,int,unordered_set<int>&)> dfs = [&](int root, int target, unordered_set<int>& seen) -> bool {
            if (memo.find({root, target}) == memo.end()) {
                if (target == root) return memo[{root, target}] = true;
                for (int neighbor : edges[root]) {
                    if (seen.find(neighbor) == seen.end()) {
                        seen.insert(neighbor);
                        if (dfs(neighbor, target, seen)) return memo[{root, target}] = true;
                    }
                }
                return memo[{root, target}] = false;
            } else  {
                return memo[{root, target}];
            }
        };
        vector<bool> ans(queries.size());
        for (int i = 0; i < queries.size(); ++i) {
            unordered_set<int> seen;
            ans[i] = dfs(queries[i][1], queries[i][0], seen);
        }
        return ans;
    }
};
```

The Discuss section mentions that the [Floyd-Warshall algorithm](https://zh.wikipedia.org/wiki/Floyd-Warshall%E7%AE%97%E6%B3%95) can be used to compute the shortest distance between two nodes in a directed graph.

## 1463. Cherry Pickup II

DP.
`dp[i][first][second]`: when the robots reach row `i`, with the first robot at `first` and the second at `second`, the maximum number of cherries picked.

`dp[i][first][second] = max(dp[i-1][for last_first in range(first - 1, first + 2)])[for last_second in range(second - 1, second + 2)] + (grid[i][first] + grid[i][second]) if first != second else grid[i][first]`.

Time complexity: O(rows * cols^2 * 9),
Space complexity: O(rows * cols^2) -> O(cols^2).

```cpp
class Solution {
    const int INF = 0x3f3f3f3f;
public:
    int cherryPickup(vector<vector<int>>& grid) {
        const int rows = grid.size();
        const int cols = grid[0].size();
        vector<vector<vector<int>>> dp (rows, vector<vector<int>>(cols, vector<int>(cols, -INF)));
        dp[0][0][cols-1] = grid[0][0] + grid[0][cols-1];
        vector<int> dj = {-1, 0, 1};
        for (int i = 1; i < rows; ++i) {
            for (int first = 0; first < cols; ++first) {
                for (int second = 0; second < cols; ++second) {
                    int add =  first == second ? grid[i][first] : grid[i][first] + grid[i][second];
                    for (int dfirst : dj) {
                        int last_first = first + dfirst;
                        if (last_first >= 0 && last_first < cols) {
                            for (int dsecond : dj) {
                                int last_second = second + dsecond;
                                if (last_second >= 0 && last_second < cols) {
                                    dp[i][first][second] = max(dp[i][first][second], dp[i-1][last_first][last_second] + add);
                                }
                            }
                        }
                    }
                }
            }
        }
        int ans = 0;
        for (int first = 0; first < cols; ++first) {
            for (int second = 0; second < cols; ++second) {
                ans = max(ans, dp[rows-1][first][second]);
            }
        }
        return ans;
    }
};
```
