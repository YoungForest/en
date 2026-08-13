---
title: LeetCode Biweekly Contest 26
date: 2020-05-17 10:15:47
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/05/17/LeetCode-biweekly-contest-26/
  en: https://youngforest.github.io/en/2020/05/17/LeetCode-biweekly-contest-26/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 115 / 7795 |	YoungForest | 19 | 	0:33:49 |  0:03:55 |   0:08:25 | 0:11:57 |   0:28:49  1 |

The week before last was the May Day holiday, so I skipped one biweekly contest. I will still try to participate as much as possible afterward. Recently, contests have been feeling smoother and smoother, whether in terms of each contest ranking, rating, or ranking in the Cruel group. All of them have improved compared with before. In particular, I want to thank the Cruel group for its daily problem and occasional lectures, which gave me more confidence in solving DP and many Hard problems. Here I recommend the [Cruel LeetCode group](https://wisdompeak.github.io/lc-score-board/) again.

## 1446. Consecutive Characters

Sliding window, maintaining a window whose characters are all equal.

Time complexity: O(N),
space complexity: O(1).

```cpp
class Solution {
public:
    int maxPower(string s) {
        const int n = s.size();
        int l = 0, r = 0;
        int ans = 1;
        for (; r < s.size(); ++r) {
            if (s[r] == s[l]) continue;
            else {
                ans = max(ans, r - l);
                l = r;
            }
        }
        ans = max(ans, n - l);
        return ans;
    }
};
```

## 1447. Simplified Fractions

Enumerate all denominators and numerators, and judge whether each fraction is in simplest form. Fortunately, C++17 already provides the built-in `gcd` function.

Time complexity: O(n^2),
space complexity: O(n).

```cpp
class Solution {
public:
    vector<string> simplifiedFractions(int n) {
        vector<string> ans;
        for (int down = 2; down <= n; ++down) {
            for (int up = 1; up < down; ++up) {
                if (gcd(up, down) == 1) {
                    ans.push_back(to_string(up) + "/" + to_string(down));
                }
            }
        }
        return ans;
    }
};
```

## 1448. Count Good Nodes in Binary Tree

DFS, passing in the maximum value among ancestor nodes as a parameter.

Time complexity: O(n),
space complexity: O(n).

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
    const int INF = 0x3f3f3f3f;
    int recurse(TreeNode* root, int maxValue) {
        if (!root) return 0;
        int ans = 0;
        if (root->val >= maxValue) ans = 1;
        maxValue = max(maxValue, root->val);
        return recurse(root->left, maxValue) + recurse(root->right, maxValue) + ans;
    }
public:
    int goodNodes(TreeNode* root) {
        return recurse(root, -INF);
    }
};
```

## 1449. Form Largest Integer With Digits That Add up to Target

DP. This is a knapsack problem, trying to add one digit each time.
We can use a Greedy idea for constant-factor optimization: if two numbers have the same cost, only the larger number is useful.

Time complexity: O(target * target * 9),
space complexity: O(target * target).

```python
class Solution:
    def largestNumber(self, cost: List[int], target: int) -> str:
        unique = {}
        seen = set()
        for i in range(len(cost) - 1, -1, -1):
            if cost[i] not in seen:
                seen.add(cost[i])
                unique[i + 1] = cost[i]
        @lru_cache(None)
        def dp(t: int) -> int:
            if t < 0:
                return -1
            if t == 0:
                return 0
            ans = -1
            for i in unique:
                tmp = dp(t - unique[i])
                if tmp >= 0:
                    ans = max(ans,  tmp * 10 + i)
            return ans
        ans = dp(target)
        return str(ans) if ans >= 0 else '0'
```

There is an `O(target)` solution in the [Discuss](<https://leetcode.com/problems/form-largest-integer-with-digits-that-add-up-to-target/discuss/635267/C%2B%2BJavaPython-Strict-O(Target)>). The main idea is to no longer store the largest string in `dp`; instead, store only the number of digits, and then construct the result backward.
