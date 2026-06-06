---
title: LeetCode weekly contest 193
date: 2020-06-15 12:19:06
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 1854 / 13794 | YoungForest | 12 | 1:18:35 |  0:15:31 | 0:12:31 | 1:18:35	 | null |

My contest ability has declined recently. In last night's biweekly contest, I also failed to solve one Q3, and now I failed to solve the last problem. I am not familiar with binary lifting on trees for Q4.

## 1480. Running Sum of 1d Array

A warm-up problem. One pass with prefix sum.

We can also use STL's [partial_sum](https://leetcode.com/problems/running-sum-of-1d-array/discuss/686276/C++-partial_sum) to achieve the same effect.

Time complexity: O(N),
Space complexity: O(N).

```cpp
class Solution {
public:
    vector<int> runningSum(vector<int>& nums) {
        vector<int> presum;
        presum.reserve(nums.size() + 1);
        presum.push_back(0);
        for (int i : nums) {
            presum.push_back(presum.back() + i);
        }
        presum.erase(presum.begin());
        return presum;
    }
};
```

## 1481. Least Number of Unique Integers after K Removals

Greedy.

Sort by frequency from small to large, and delete the smaller frequencies first.

Time complexity: O(N + N log N + K),
Space complexity: O(N).

```cpp
class Solution {
public:
    int findLeastNumOfUniqueInts(vector<int>& arr, int k) {
        unordered_map<int,int> count;
        for (int i : arr) {
            ++count[i];
        }
        vector<int> frequence;
        frequence.reserve(count.size());
        for (const auto& p : count) {
            frequence.push_back(p.second);
        }
        sort(frequence.begin(), frequence.end());
        int i = 0;
        const int n = frequence.size();
        if (i == k) return n;
        for (int j = 0; j < frequence.size(); ++j) {
            i += frequence[j];
            if (i == k) {
                return n - j - 1;
            } else if (i > k) {
                return n - j;
            }
        }
        return 0;
    }
};
```

## 1482. Minimum Number of Days to Make m Bouquets

Transform the optimization problem into a binary-search decision problem.

Find a critical point: before the critical point the decision problem is false, and after it the decision problem is true.

Time complexity: O(log 1e9 * N),
Space complexity: O(1).

```cpp
class Solution {
public:
    int minDays(vector<int>& bloomDay, int m, int k) {
        auto determine = [&](int x) -> bool {
            int sub = 0;
            int ans = 0;
            for (int i : bloomDay) {
                if (i <= x) {
                    ++sub;
                } else {
                    ans += sub / k;
                    sub = 0;
                }
            }
            ans += sub / k;
            return ans >= m;
        };
        int lo = 1, hi = 1e9 + 1;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (determine(mid)) {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        return lo > 1e9 ? -1 : lo;
    }
};
```

## 1483. Kth Ancestor of a Tree Node

Similar to [binary lifting for LCA on trees](https://cp-algorithms.com/graph/lca_binary_lifting.html). Trade space for time.

Time complexity: O(N * log N),
Space complexity: O(N * log N).

```cpp
class TreeAncestor {
    vector<vector<int>> ancestors;
public:
    TreeAncestor(int n, vector<int>& parent) {
        // time: N * log N
        vector<vector<int>> children(n);
        for (int i = 1; i < n; ++i) {
            children[parent[i]].push_back(i);
        }
        ancestors.resize(n);
        function<void(int,vector<int>&)> dfs = [&](int root, vector<int>& path) -> void {
            int depth = path.size();
            for (int i = 0; depth - (1 << i) >= 0; ++i) {
                ancestors[root].push_back(path[depth - (1 << i)]);
            }
            path.push_back(root);
            for (int child : children[root]) {
                dfs(child, path);
            }
            path.pop_back();
        };
        vector<int> path;
        path.push_back(-1);
        dfs(0, path);
    }
    
    int getKthAncestor(int node, int k) {
        // time: log K
        int i = 20;
        while (k > 0 && node != -1) {
            if ((k & (1 << i)) != 0) {
                if (i < ancestors[node].size())
                    node = ancestors[node][i];
                else
                    node = -1;
                k -= (1 << i);
            }
            --i;
        }
        return node;
    }
};

/**
 * Your TreeAncestor object will be instantiated and called as such:
 * TreeAncestor* obj = new TreeAncestor(n, parent);
 * int param_1 = obj->getKthAncestor(node,k);
 */
```
