---
title: LeetCode Weekly Contest 250
date: 2021-07-19 09:20:38
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/07/19/LeetCode-weekly-contest-250/
  en: https://youngforest.github.io/en/2021/07/19/LeetCode-weekly-contest-250/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (6) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 192 / 13694 | YoungForest | 18 | 	1:22:27 |  0:03:20 | 0:09:15  🐞1 | 0:28:08 | 1:07:27  🐞2 |

My results have been pretty good for two consecutive weeks, both in the top 200. As a result, the rating of my US-site alt account is about to reach 2400 too. Future contests will require even more caution.

## 1935. Maximum Number of Words You Can Type

A warm-up problem. Using Python for string problems really is the right choice. `split` alone makes it worth it.

```python
class Solution:
    def canBeTypedWords(self, text: str, brokenLetters: str) -> int:
        ans = 0
        broken = set()
        for i in brokenLetters:
            broken.add(i)
        def ok(word):
            for c in word:
                if c in broken:
                    return False
            return True
        for word in text.split(' '):
            if ok(word):
                ans += 1
        return ans
```

Time complexity: O(text.length),
space complexity: O(text.length + 26).

## 1936. Add Minimum Number of Rungs

Greedy. If the next rung cannot be reached, add one at the farthest reachable distance.

One thing to note: do not add rungs one by one. Use division to add all missing intermediate rungs at once. Otherwise it will TLE, and I received a 5-minute penalty because of this.

```cpp
class Solution {
public:
    int addRungs(vector<int>& rungs, int dist) {
        int ans = 0;
        
        int last = 0;
        for (int idx = 0; idx < rungs.size(); ++idx) {
            const int i = rungs[idx];
            if (i - last <= dist) {
                last = i;
            } else {
                ans += (i - last - 1) / dist;
                last = i;
            }
        }
        
        return ans;
    }
};
```

Time complexity: O(rungs.length),
space complexity: O(1).

## 1937. Maximum Number of Points with Cost

This is obviously a Dynamic Programming problem.

```
dp[i][j] = points[i][j] + max(dp[i-1][k] - abs(k-j) for k in range(n))
```

However, if implemented directly, the time complexity is O(m * n ^ 2), which will obviously TLE. Optimization is needed.

For the previous row, based on the sign inside `abs`, it can be split into:

The earlier part: dp[i-1] - (j - k) = dp[i-1] + k - j,

The later part: dp[i-1] - (k - j) = dp[i-1] - k + j.

Two TreeSets can be used to record `dp + k` and `dp - k` for the front and back parts, and update these two TreeSets as `j` is updated.

This reduces the time complexity to O(m * n).

```cpp
class Solution {
    using ll = long long;
public:
    long long maxPoints(vector<vector<int>>& points) {
        const int m = points.size();
        const int n = points[0].size();
        vector<vector<ll>> dp(m, vector<ll> (n, 0));
        // i == 0
        for (int j = 0; j < n; ++j) {
            dp[0][j] = points[0][j];
        }
        for (int i = 1; i < m; ++i) {
            multiset<ll> before, after;
            for (int k = 0; k < n; ++k) {
                after.insert(dp[i-1][k] - k);
            }
            for (int j = 0; j < n; ++j) {
                ll add = 0;
                if (before.empty()) {
                    add = *after.rbegin() + j;
                } else if (after.empty()) {
                    add = *before.rbegin() - j;
                } else {
                    add = max(*before.rbegin() - j, *after.rbegin() + j);
                }
                dp[i][j] = max(dp[i][j], points[i][j] + add);
                auto it = after.find(dp[i-1][j] - j);
                after.erase(it);
                before.insert(dp[i-1][j] + j);
            }
        }
        
        
        return *max_element(dp.back().begin(), dp.back().end());
    }
};
```

Time complexity: O(m * n),
space complexity: O(m * n). It can actually be reduced to O(n), but that is a bit more troublesome to write and unnecessary for AC.

## 1938. Maximum Genetic Difference Query

Trie + backtracking + offline computation.

The Trie is used to quickly compute the maximum XOR, backtracking is used to maintain the path from the root to the current node and update the Trie, and offline computation is used to obtain the answers to queries.

When using C++, pay attention to the Trie implementation.

In this problem, using `shared_ptr` will TLE, and raw pointer + delete will also TLE.

It only AC'd after I removed `delete`. This is practically forcing me to leak memory.

However, because this problem can use `cnt` to represent node state, it actually does not need to truly delete nodes.

```cpp
class Solution {
    const int MAX_BIT = 17;
    struct TrieNode {
        array<TrieNode*, 2> children;
        int cnt = 0;
    };
    void buildTrie(const int num, TrieNode* root, const int index) {
        if (index < 0) return;
        const int b = (num >> index) & 1;
        if (root->children[b] == nullptr) {
            root->children[b] = new TrieNode();
        }
        root->children[b]->cnt++;
        buildTrie(num, root->children[b], index - 1);
    }
    void eraseTrie(const int num, TrieNode* root, const int index) {
        if (index < 0) return;
        const int b = (num >> index) & 1;
        // if (root->children[b] == nullptr) {
        //     cout << num << ":" << index << " " << endl;
        //     root->children[b] = make_shared<TrieNode>();
        // }
        eraseTrie(num, root->children[b], index - 1);
        root->children[b]->cnt--;
        if (root->children[b]->cnt == 0) {
            // delete root->children[b];
            root->children[b] = nullptr;
        }
    }
public:
    vector<int> maxGeneticDifference(vector<int>& parents, vector<vector<int>>& queries) {
        const int n = parents.size();
        const int m = queries.size();
        vector<vector<int>> children(n);
        int rootNode = -1;
        for (int i = 0; i < n; ++i) {
            if (parents[i] == -1) rootNode = i;
            else children[parents[i]].push_back(i);
        }
        vector<int> ans(m);
        using pii = pair<int, int>;
        vector<vector<pii>> offQueies(n);
        for (int index = 0; index < m; ++index) {
            offQueies[queries[index][0]].push_back({queries[index][1], index});
        }
        // shared_ptr<TrieNode> root = make_shared<TrieNode>();
        auto root = new TrieNode();
        auto maxXOR = [&](const int val) -> int {
            int ans = 0;
            auto current = root;
            for (int i = MAX_BIT; i >= 0; --i) {
                const int b = (val >> i) & 1;
                if (current->children[1 - b]) {
                    ans += (1 << i);
                    current = current->children[1 - b];
                } else {
                    current = current->children[b];
                }
            }
            return ans;
        };
        function<void(const int, vector<int>&)> dfs = [&](const int current, vector<int>& path) -> void {
            for (auto p : offQueies[current]) {
                int val = get<0>(p);
                int index = get<1>(p);
                ans[index] = maxXOR(val);
            }
            // cout << current << " ";
            for (int child : children[current]) {
                path.push_back(child);
                buildTrie(child, root, MAX_BIT);
                dfs(child, path);
                eraseTrie(path.back(), root, MAX_BIT);
                path.pop_back();
            }
        };
        vector<int> path;
        path.push_back(rootNode);
        buildTrie(rootNode, root, MAX_BIT);
        dfs(rootNode, path);
        eraseTrie(path.back(), root, MAX_BIT);
        path.pop_back();
        return ans;
    }
};
```

Time complexity: O(log (max(val_i)) * (parents.length + queries.length)),
space complexity: O(parents.length). The space consumed by the Trie is actually the sum of a geometric sequence.
