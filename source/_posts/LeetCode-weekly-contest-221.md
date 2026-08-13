---
title: LeetCode weekly contest 221
date: 2020-12-27 11:59:37
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/12/27/LeetCode-weekly-contest-221/
  en: https://youngforest.github.io/en/2020/12/27/LeetCode-weekly-contest-221/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 231 / 8838 | YoungForest | 18 | 1:24:39 | 0:03:55	 | 0:21:00 | 0:30:16 | 1:09:39  3 |

## 5637. Determine if String Halves Are Alike

A warm-up problem. Use a set to record vowels, then count one by one.

```cpp
class Solution {
public:
    bool halvesAreAlike(string s) {
        unordered_set<char> yuan = {'a', 'e', 'i', 'o', 'u',
                                    'A', 'E', 'I', 'O', 'U'};
        const int n = s.size();
        int first = 0;
        for (int i = 0; i < n / 2; ++i) {
            if (yuan.find(s[i]) != yuan.end()) ++first;
        }
        int last = 0;
        for (int i = n / 2; i < n; ++i) {
            if (yuan.find(s[i]) != yuan.end()) ++last;
        }
        return first == last;
    }
};
```

Time complexity: O(N),
Space complexity: O(1).

## 5638. Maximum Number of Eaten Apples

Greedy. Eat the apples that are about to expire first.

Use `TreeMap` to maintain expiration time, and binary search to find the apple with the nearest expiration time.

```cpp
class Solution {
public:
    int eatenApples(vector<int>& apples, vector<int>& days) {
        const int n = apples.size();
        int maxDay = n;
        for (int i = 0; i < n; ++i) {
            maxDay = max(maxDay, i + days[i]);
        }
        map<int, int> rust;
        int ans = 0;
        // cout << "maxDay: " << maxDay << endl;
        for (int i = 0; i < maxDay; ++i) {
            if (i < n && apples[i] > 0) {
                rust[i + days[i] - 1] += apples[i];
            }
            auto it = rust.lower_bound(i);
            if (it != rust.end()) {
                // cout << i << endl;
                ++ans;
                --it->second;
                if (it->second == 0) {
                    rust.erase(it);
                }
            }
        }
        return ans;
    }
};
```

Time complexity: O(N),
Space complexity: O(N).

## 1706. Where Will the Ball Fall

Since `m` and `n` are small, we can try a brute-force DFS solution.

```cpp
class Solution {
public:
    vector<int> findBall(vector<vector<int>>& grid) {
        const int m = grid.size();
        const int n = grid[0].size();
        function<int(const int, const int)> dfs = [&](const int row, const int col) -> int {
            if (row == m) {
                return col;
            } else {
                if (grid[row][col] == 1) {
                    if (col == n - 1 || grid[row][col + 1] == -1) {
                        return -1;
                    }
                    return dfs(row + 1, col + 1);
                } else {
                    if (col == 0 || grid[row][col - 1] == 1) {
                        return -1;
                    }
                    return dfs(row + 1, col - 1);
                }
            }
        };
        vector<int> ans(n);
        for (int i = 0; i < n; ++i) {
            ans[i] = dfs(0, i);
        }
        return ans;
    }
};
```

Time complexity: O(n * m).

## 5640. Maximum XOR With an Element From Array

Classic 0-1 Trie.

In the last problem, I was trapped by smart pointer `shared_ptr` and got TLE three times. After switching to raw pointers, it passed. Although the cost of smart pointers is small, `shared_ptr` is not zero-overhead. Even so, being blocked by LeetCode constant factors is still so annoying!

```cpp
class Solution {
    struct TrieNode {
        TrieNode* zero = nullptr;
        TrieNode* one = nullptr;
        int count = 0;
        int minValue = numeric_limits<int>::max();
    };
    void insert(TrieNode* root, const int i, const int num) {
        ++root->count;
        root->minValue = min(root->minValue, num);
        if (i >= 0) {
            if (num & (1 << i)) {
                if (root->one == nullptr) root->one = new TrieNode();
                insert(root->one, i - 1, num);
            } else {
                if (root->zero == nullptr) root->zero = new TrieNode();
                insert(root->zero, i - 1, num);
            }
        }
    }
    int recurseQuery(TrieNode* root, const int x, const int limit, const int i, const int ans) {
        // cout << "level: " << i << " ";
        if (i < 0) return ans;
        if (x & (1 << i)) { // 1
            if (root->zero && root->zero->count > 0) {
                // cout << "zero" << endl;
                return recurseQuery(root->zero, x, limit, i - 1, ans | (1 << i));
            } else if (root->one && root->one->count > 0) {
                // cout << "one" << endl;
                return recurseQuery(root->one, x, limit, i - 1, ans);
            } else {
                return -1;
            }
        } else { // 0
            if (root->one && root->one->count > 0 && root->one->minValue <= limit) {
                // cout << "root->one->minValue " << root->one->minValue << " " << limit << " ";
                // cout << "one" << endl;
                return recurseQuery(root->one, x, limit, i - 1, ans | (1 << i));
            } else if (root->zero && root->zero->count > 0) {
                // cout << "zero" << endl;
                return recurseQuery(root->zero, x, limit, i - 1, ans);
            } else {
                return -1;
            }
        }
    }
public:
    vector<int> maximizeXor(vector<int>& nums, vector<vector<int>>& queries) {
        const int maxDigit = 29;
        TrieNode* root = new TrieNode();
        for (int i : nums) {
            insert(root, maxDigit, i);
        }
        const int q = queries.size();
        vector<int> ans(q, -1);
        for (int i = 0; i < q; ++i) {
            const int x = queries[i][0];
            const int m = queries[i][1];
            if (root->minValue > m) {
                ans[i] = -1;
            } else {
                ans[i] = recurseQuery(root, x, m, maxDigit, 0);
            }
        }
        return ans;
    }
};
```

Time complexity: O(29 * N),
Space complexity: O(29 * N).
