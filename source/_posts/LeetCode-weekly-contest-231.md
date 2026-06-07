---
title: LeetCode Weekly Contest 231
date: 2021-03-07 16:06:05
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 221 / 12900 | YoungForest | 13 | 	0:26:48 |  0:03:30 |  0:08:23  1 | 0:21:48 | null |

A 3-problem contestant, again. sad.

<!-- more -->

## 1784. Check if Binary String Has at Most One Segment of Ones

Warm-up problem. Finite state machine.

```cpp
class Solution {
public:
    bool checkOnesSegment(string s) {
        int state = 0;
        // 0: not see 1
        // 1: see 1 and now 1
        // 2: see 1 but now 0
        for (char c : s) {
            if (c == '0') {
                if (state == 0) {
                    state = 0;
                } else if (state == 1) {
                    state = 2;
                } else {
                    state = 2;
                }
            } else { // c == '1'
                if (state == 0) {
                    state = 1;
                } else if (state == 1) {
                    state = 1;
                } else {
                    return false;
                }
            }
        }
        return true;
    }
};
```

Time complexity: O(N),
space complexity: O(1).

## 1785. Minimum Elements to Add to Form a Given Sum

Greedy. Add or subtract `limit`/`-limit` each time, using the largest step size to get the minimum number of steps.
Note: the constraints intentionally set a trap here. Using `int` to sum `nums` will overflow. Of course, `Python` does not have this issue.

```cpp
class Solution {
    using ll = long long;
public:
    int minElements(vector<int>& nums, ll limit, ll goal) {
        const ll current = accumulate(nums.begin(), nums.end(), 0L);
        if (current > goal) {
            return (current - goal) / limit + (((current - goal) % limit == 0) ? 0 : 1);
        } else if (current == goal) {
            return 0;
        } else {
            return (goal - current) / limit + (((goal - current) % limit == 0) ? 0 : 1);
        }
    }
};
```

## 1786. Number of Restricted Paths From First to Last Node

The problem statement is hard to understand, but the problem itself is not difficult.
Use `Dijkstra` to compute `distanceToLastNode(x)`, then a memoized `dfs` is enough.

```cpp
class Solution {
    using pii = pair<int, int>;
    const int MOD = 1e9 + 7;
public:
    int countRestrictedPaths(int n, vector<vector<int>>& edges) {
        // dijistra: E log E + V
        vector<vector<pii>> neighbors(n+1);
        for (const auto& e : edges) {
            neighbors[e[0]].push_back({e[1], e[2]});
            neighbors[e[1]].push_back({e[0], e[2]});
        }
        
        vector<int> distance(n + 1, 0);
        priority_queue<pii, vector<pii>, greater<>> pq;
        vector<bool> visited(n + 1, false);
        pq.push({0, n});
        while (!pq.empty()) {
            auto [d, current] = pq.top();
            pq.pop();
            if (!visited[current]) {
                visited[current] = true;
                distance[current] = d;
                for (auto neighbor : neighbors[current]) {
                    pq.push({d + neighbor.second, neighbor.first});
                }
            }
        }
        
        vector<int> memo(n + 1, -1);
        function<int(const int)> dfs = [&](const int current) -> int {
            if (current == n) {
                return memo[current] = 1;
            } else {
                if (memo[current] != -1) return memo[current];
                int ans = 0;
                for (auto neighbor : neighbors[current]) {
                    const int node = neighbor.first;
                    if (distance[node] < distance[current]) {
                        ans = (ans + dfs(node)) % MOD;
                    }
                }
                return memo[current] = ans;
            }
        };
        
        return dfs(1);
    }
};
```

Time complexity: O(E log E + V + E),
space complexity: O(V + E).

## [1787. Make the XOR of All Segments Equal to Zero](https://leetcode.com/problems/make-the-xor-of-all-segments-equal-to-zero/) [Chinese](https://leetcode-cn.com/problems/make-the-xor-of-all-segments-equal-to-zero/)

To be honest, I thought about this problem for a long time and had no idea. I even searched Google for quite a while and felt it might be an original known problem. But I did not find it.
After the contest, someone in the group posted a link, and it really was an original problem:
[original problem link](https://stackoverflow.com/questions/64186699/minimum-changes-so-the-xor-of-every-k-consecutive-elements-is-0).
It seems searching for problems is also a skill. Some people can find it and copy the code, and that also counts as their ability.

I referenced [the group owner's solution](https://www.bilibili.com/video/BV1vz4y1175J/).

First, we can observe that in the final state, every `k` numbers repeat. Therefore, we can determine that we only need to compute the first `k` numbers so their `XOR` is 0. Of course, when calculating `cost`, we still need to consider the later numbers.

Then, based on the constraints, guess that this is a DP with time complexity O(1024*n). Then construct the state transition equation.

```cpp
class Solution {
    const int MAX_NUMS = 1 << 10;
public:
    int minChanges(vector<int>& nums, int k) {
        const int n = nums.size();
        
        // the total size of Set[i]
        vector<int> totalCost(k);
        // cnt[i][j]: the count of j in Set[i]
        vector<unordered_map<int, int>> cnt(k);
        
        for (int i = 0; i < n; ++i) {
            ++totalCost[i % k];
            ++cnt[i%k][nums[i]];
        }
        
        // the cost to set Set[i] to v
        auto cost = [&](const int i, const int v) -> int {
            return totalCost[i] - cnt[i][v];
        };
        
        // dp[i][d]: the minimum cost to make first i XOR equal to d
        vector<vector<int>> dp(k, vector<int> (MAX_NUMS, numeric_limits<int>::max()));
        int minLastDp = numeric_limits<int>::max();
        for (int d = 0; d < MAX_NUMS; ++d) {
            dp[0][d] = totalCost[0] - cnt[0][d];
            minLastDp = min(minLastDp, dp[0][d]);
        }
        for (int i = 1; i < k; ++i) {
            int minDp = numeric_limits<int>::max(); 
            for (int d = 0; d < MAX_NUMS; ++d) {
                dp[i][d] = minLastDp + totalCost[i];
                for (int j = i; j < n; j += k) {
                    dp[i][d] = min(dp[i][d], 
                                  dp[i-1][d^nums[j]] + cost(i, nums[j]));
                }
                minDp = min(minDp, dp[i][d]);
            }
            minLastDp = minDp;
        }
        return dp[k-1][0];
    }
};
```

Time complexity: O(k * 1024 * n / k) = O(1024 * n),
space complexity: O(k * 1024).
