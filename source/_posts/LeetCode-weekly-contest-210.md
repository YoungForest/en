---
title: LeetCode Weekly Contest 210
date: 2020-10-11 19:23:21
description: "Four solutions cover nesting depth, city network rank, cross-string palindromes, and connected subtrees grouped by diameter."
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/10/11/LeetCode-weekly-contest-210/
  en: https://youngforest.github.io/en/2020/10/11/LeetCode-weekly-contest-210/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 94 / 11792 | YoungForest | 18 | 0:51:30 | 0:03:13 | 0:08:36 | 0:21:59 | 0:51:30 |

All the problems in this contest were routine problems. I did not run into real difficulties and finally made it into the top 100 again after a long while. It was hard-won. My ranking in the Cruel group also rose to 25 because of it.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-210/en-hero.webp" alt="A civic observatory layers nested hoops, road hubs, a mirrored splice ribbon and tree neighbourhoods measured end to end" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## 5535. Maximum Nesting Depth of the Parentheses

A warm-up problem. For the nesting depth of parentheses, a stack-style idea is enough. Push on a left parenthesis and pop on a right parenthesis.

```cpp
class Solution {
public:
    int maxDepth(string s) {
        int left = 0;
        int ans = 0;
        for (char c : s) {
            if (c == '(') {
                ++left;
            } else if (c == ')') {
                --left;
            }
            ans = max(ans, left);
        }
        return ans;
    }
};
```

Time complexity: O(N),
space complexity: O(1).

## 5536. Maximal Network Rank

Brute force. Enumerate every city pair and calculate the network rank. One trick is to first count the degree of each city, then subtract one if there is a road between the two cities.

```cpp
class Solution {
public:
    int maximalNetworkRank(int n, vector<vector<int>>& roads) {
        using pii = pair<int, int>;
        set<pii> roadmap;
        vector<int> cnt(n);
        for (const auto& r : roads) {
            roadmap.insert({r[0], r[1]});
            ++cnt[r[0]];
            ++cnt[r[1]];
        }
        int ans = 0;
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                int sub = 0;
                if (roadmap.find({i, j}) != roadmap.end() || roadmap.find({j, i}) != roadmap.end()) {
                    ++sub;
                }
                ans = max(ans, cnt[i] + cnt[j] - sub);
            }
        }
        return ans;
    }
};
```

Time complexity: O(n^2 * log m + m log m),
space complexity: O(n + m).

There is also a [legendary O(n + m) solution](https://leetcode-cn.com/problems/maximal-network-rank/solution/onm-mei-ju-fa-by-zerotrac2/) in the discussion section. It is really strong. The rough idea is that we only need to know the cities with the largest and second-largest degrees.

## 1616. Split Two Strings to Make Palindrome

By observation, there are only two possible kinds of answers:
a_prefix + a_mid + b_suffix,
a_prefix + b_mid + b_suffix,
b_prefix + a_mid + a_suffix,
b_prefix + b_mid + a_suffix.

Compute the longest matched length between `a_prefix` and `b_suffix`, and the longest length of `a_mid`/`b_mid`, then check whether they intersect.
The same applies to the longest matched length between `b_prefix` and `a_suffix`.

```cpp
class Solution {
    int head1 = 0;
    int head2 = 0;
    bool check(const string& a, const string& b) {
        const int n = a.size();
        int left, right;
        if (n % 2 == 0) {
            left = (n - 1)  / 2;
            right = n / 2;
        } else {
            const int mid = n / 2;
            left = mid - 1;
            right = mid + 1;
        }
        while (left >= 0 && right < n && a[left] == a[right]) {
            --left;
            ++right;
        }
        const int length = right - left - 1;
        return head1 > left || head2 > left;
    }
public:
    bool checkPalindromeFormation(string a, string b) {
        const int n = a.size();
        while (head1 < n && a[head1] == b[n - 1 - head1]) {
            ++head1;
        }
        while (head2 < n && b[head2] == a[n - 1 - head2]) {
            ++head2;
        }
        return check(a, b) || check(b, a);
    }
};
```

Time complexity: O(N),
space complexity: O(1).

## 5538. Count Subtrees With Max Distance Between Cities

Brute force, and I wrote nearly 100 lines of code. First enumerate every combination of nodes, check whether it is a subtree, and if so calculate the maximum distance.

To check whether it is a subtree, I used Union-Find to determine whether all nodes are connected.
For the maximum distance `d`, I used multiple DFS traversals, giving time complexity `n^2`. After the contest, I learned that there is an [O(n) two-DFS method for computing a tree diameter](https://oi-wiki.org/graph/tree-diameter/).

```cpp
class Solution {
    struct UF {
        vector<int> parent;
        int count;
        UF(int n) : parent(n), count(n) {
            iota(parent.begin(), parent.end(), 0);
        }
        // A utility function to find the subset of an element i  
        int find(int x)  
        {  
            return x == parent[x] ? x : parent[x] = find(parent[x]);
        }  

        // A utility function to do union of two subsets  
        void unite(int x, int y)  
        {  
            int xset = find(x);  
            int yset = find(y);  
            if(xset != yset) 
            {  
                parent[xset] = yset;
                --count;
            }  
        }    
    };
public:
    vector<int> countSubgraphsForEachDiameter(int n, vector<vector<int>>& edges) {
        // time: (n ^ 2 * 2 ^ n)
        vector<unordered_set<int>> graph(n);
        for (const auto& v : edges) {
            const int a = v[0] - 1;
            const int b = v[1] - 1;
            graph[a].insert(b);
            graph[b].insert(a);
        }
        vector<vector<int>> dist(n, vector<int>(n));
        function<void(const int, const int, const int, const int)> dfs = [&](const int root, const int current, const int parent, const int depth) -> void {
            dist[root][current] = depth;
            for (int child : graph[current]) {
                if (child == parent) continue;
                dfs(root, child, current, depth + 1);
            }
                
        };
        for (int i = 0; i < n; ++i) {
            dfs(i, i, -1, 0);
        }
        auto getnodes = [&](const int mask) -> vector<int> {
            vector<int> ans;
            for (int i = 0; i < n; ++i) {
                if ((mask & (1 << i)) != 0) {
                    ans.push_back(i);
                }
            }
            return ans;
        };
        auto checksubtree = [&](const vector<int>& nodes) -> bool {
            if (nodes.empty() || nodes.size() == 1) return false;
            UF uf(n);
            for (int i = 0; i < nodes.size(); ++i) {
                for (int j = i + 1; j < nodes.size(); ++j) {
                    const int a = nodes[i], b = nodes[j];
                    if (graph[a].find(b) != graph[a].end()) {
                        uf.unite(a, b);
                    }
                }
            }
            int root = -1;
            for (int a : nodes) {
                if (root == -1) {
                    root = uf.find(a);
                } else {
                    if (root != uf.find(a)) return false;
                }
            }
            return true;
        };
        auto distance = [&](const vector<int>& nodes) -> int {
            int ans = 0;
            for (int i = 0; i < nodes.size(); ++i) {
                for (int j = i + 1; j < nodes.size(); ++j) {
                    const int a = nodes[i], b = nodes[j];
                    ans = max(ans, dist[a][b]);
                }
            }
            return ans;
        };
        // d = 1...n-1        
        vector<int> ans (n - 1, 0);
        const int upper = (1 << n);
        for (int mask = 0; mask < upper; ++mask) {
            auto nodes = getnodes(mask);
            if (!checksubtree(nodes)) continue;
            ++ans[distance(nodes) - 1];
        }
        return ans;
    }
};
```

Time complexity: O(n ^ 2 * 2 ^ n),
space complexity: O(n ^ 2).

Since `n <= 15`, exponential complexity can still pass.

There is also an [`O(n ^ 5)` solution](https://leetcode-cn.com/problems/count-subtrees-with-max-distance-between-cities/solution/python3-shu-xing-dp-by-simpleson/) in the discussion section.
