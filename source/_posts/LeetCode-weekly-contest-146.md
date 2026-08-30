---
title: LeetCode weekly contest 146
date: 2019-07-21 22:49:39
description: A day-out, night-finish review of domino normalisation, alternating paths, minimum-cost leaf trees and sign enumeration.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/07/21/LeetCode-weekly-contest-146/
  en: https://youngforest.github.io/en/2019/07/21/LeetCode-weekly-contest-146/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-146/en-hero.webp" alt="Forest returns from a daytime reunion to rotate dominoes, trace alternating paths, lift large leaves and flip three sign levers at night" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

Today my high school classmate xl came to Beijing to chat with me. I had lunch and dinner with him and hcq, and we chatted for the whole afternoon. During the morning contest, I only hurriedly finished the warm-up problem. For the second problem, because of carelessness, I wrote the timing of the `red` change incorrectly and had no time to debug it. I did not even look at the last two problems.
After coming back at 9 p.m., I finally finished the problems, and also found the bug in the second problem.
But in terms of time, it should have been too late.
Rank 1800+.

Overall, although the problems in this contest were not hard, they required time and thinking to solve. These are also the kind of problems I like. Solving a problem that does not reveal its answer at first glance through my own thinking feels great. The thrill is better than godlike mode and triple kills.

## 5130. Number of Equivalent Domino Pairs

Since dominoes can be equal after rotation, we normalize them (map the two kinds of dominoes into one).
Then just compute the number of combinations.

Time complexity: O(N log N), because `pair` is not hashable by default, so I lazily used `map`.
Space complexity: O(N).

```cpp
class Solution {
public:
    int numEquivDominoPairs(vector<vector<int>>& dominoes) {
        const int n = dominoes.size();
        map<pair<int, int>, int> transform;
        int ans = 0;
        for (int i = 0; i < n; ++i) {
            if (dominoes[i][0] < dominoes[i][1]) {
                ++transform[make_pair(dominoes[i][0], dominoes[i][1])];  
            } else {
                ++transform[make_pair(dominoes[i][1], dominoes[i][0])];
            }
        }
        for (const auto& p : transform) {
            ans += p.second * (p.second - 1) / 2;
        }
        return ans;
    }
};
```

## 1129. Shortest Path with Alternating Colors

Compute shortest paths with BFS. Because colors must alternate, the code is a little troublesome and needs two sets of variables.

Time complexity: O(N), each node is traversed at most twice.
Space complexity: O(max(N, edges)).

```cpp
class Solution {
public:
    vector<int> shortestAlternatingPaths(int n, vector<vector<int>>& red_edges, vector<vector<int>>& blue_edges) {
        unordered_map<int, vector<int>> red_to, blue_to;
        for (const auto& edge : red_edges) {
            red_to[edge[0]].push_back(edge[1]);
        }
        for (const auto& edge : blue_edges) {
            blue_to[edge[0]].push_back(edge[1]);
        }
        vector<int> ret(n, numeric_limits<int>::max());
        ret[0] = 0;
        // bfs
        for (bool red : {true, false}) {
            queue<int> q;
            unordered_set<int> seen_red, seen_blue;
            q.push(0);
            seen_red.insert(0);
            seen_blue.insert(0);
            int level = 0;
            while (!q.empty()) {
                int s = q.size();
                ++level;
                for (int i = 0; i < s; ++i) {
                    int current = q.front();
                    q.pop();
                    vector<int>* edges = nullptr;
                    unordered_set<int>* seen = nullptr;
                    if (red) {
                        edges = &red_to[current];
                        seen = &seen_red;
                    } else {
                        edges = &blue_to[current];
                        seen = &seen_blue;
                    }
                    for (int des : *edges) {
                        if (seen->find(des) == seen->end()) {
                            q.push(des);
                            seen->insert(des);
                            ret[des] = min(ret[des], level);
                        }
                    }
                }            
                red = !red;
            }
        }
        for (auto& item : ret) {
            if (item == numeric_limits<int>::max())
                item = -1;
        }
        return ret;
    }
};
```

## 5131. Minimum Cost Tree From Leaf Values

This was the hardest problem in this contest. I also spent a long time thinking before figuring it out.

First, observe:
- The relationship between the number of levels and the number of leaves. n levels have at least n leaves and at most 2^(n-1) leaves.
- Once the number of leaves is fixed, the number of non-leaf nodes is also fixed.
- By observing small trees, we can get a greedy solution. Lift larger leaves upward as much as possible, reducing their levels so they influence fewer non-leaf nodes, which minimizes the total sum.

Time complexity: O(n log n). Similar to quicksort, it divides the problem into two subproblems and still needs an end - begin combination step.
Space complexity: O(n), n leaves can have at most n levels, which is the recursion depth.

```cpp
class Solution {
    int ret = 0;
    // max_leaf
    int recurse(const vector<int>& arr, int begin, int end) {
        if (begin + 1 == end) {
            return arr[begin];
        }
        int max_index = begin;
        int max_value = arr[begin];
        for (int i = begin + 1; i < end; ++i) {
            if (arr[i] >= max_value) {
                max_value = arr[i];
                max_index = i;
            }
        }
        int l, r;
        if (max_index - begin + 2 <= end - max_index) { // 向后分
            l = recurse(arr, begin, max_index + 1);
            r = recurse(arr, max_index + 1, end);
        } else {    // 向前分
            l = recurse(arr, begin, max_index);
            r = recurse(arr, max_index, end);
        }
        ret += l * r;
        return max(l, r);
    }
public:
    int mctFromLeafValues(vector<int>& arr) {
        recurse(arr, 0, arr.size());
        return ret;
    }
};
```

## 1131. Maximum of Absolute Value Expression

First observe the data scale, `40000`, which rules out the brute-force `O(N^2)` solution.
Look for an `O(N)` or `O(N log N)` solution.
In fact, because each absolute value has two effects: unchanged or negated. There are three absolute value signs, so just enumerate 2^3 = 8 combinations.

Time complexity: O(8 * N)
Space complexity: O(N), which can easily be reduced to O(1).

```cpp
class Solution {
public:
    int maxAbsValExpr(vector<int>& arr1, vector<int>& arr2) {
        const int n = arr1.size();
        int ans = numeric_limits<int>::min();
        vector<int> tmp(n);
        for (int i : {-1, 1}) {
            for (int j : {-1, 1}) {
                for (int k : {-1, 1}) {
                    for (int l = 0; l < n; ++l) {
                        tmp[l] = arr1[l] * i + arr2[l] * j + l * k;
                    }
                    auto r = minmax_element(tmp.begin(), tmp.end());
                    ans = max(ans, *r.second - *r.first);
                }
            }
        }
        return ans;
    }
};
```
