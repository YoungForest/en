---
title: LeetCode Weekly Contest 249
date: 2021-07-11 17:54:25
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (6) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 74 / 12832 | YoungForest | 19 | 1:06:48 | 0:02:44 | 0:07:11 | 0:37:44 | 1:01:48 🐞1 |

Without noticing, I skipped updating the weekly contest blog for three weeks. Recently I was busy with graduation and onboarding. During the graduation trip in the middle, I even rarely skipped both a weekly contest and a biweekly contest.

This week counts as my first week after joining Amazon, and my global rank surprisingly reached 74. Thinking carefully, the last time I entered the top 100 in a weekly contest was Contest 243, roughly a month and a half ago.

The last two problems this week were both hard, so they really separated people.

Because my China-site rating had reached 2460, I was worried about losing rating, so recently I have basically been playing on the US site. The US-site account is a 2330 "alt account", so it is basically hard to lose points.

## 1929. Concatenation of Array

A warm-up problem. Straightforward. Python can even solve it in one line with `return nums + nums`.

```cpp
class Solution {
public:
    vector<int> getConcatenation(vector<int>& nums) {
        const int n = nums.size();
        vector<int> ans(2 * n);
        for (int i = 0; i < n; ++i) {
            ans[i] = ans[i+n] = nums[i];
        }
        return ans;
    }
};
```

Time complexity: O(n),
space complexity: O(n).

## 1930. Unique Length-3 Palindromic Subsequences

Because the palindrome length is short, only 3, there are at most 26*26 different palindromes. The palindrome can be represented by the middle character and the characters on both sides.

Because it is a subsequence, use `cntLeft` and `cntRight` to maintain whether the characters on the two sides meet the requirement.

```cpp
class Solution {
public:
    int countPalindromicSubsequence(string s) {
        vector<vector<bool>> seen(26, vector<bool>(26, false));
        int ans = 0;
        vector<int> cntRight(26, 0);
        for (char c : s) {
            ++cntRight[c - 'a'];
        }
        vector<int> cntLeft(26, 0);
        for (char c : s) {
            --cntRight[c - 'a'];
            for (char i = 'a'; i <= 'z'; ++i) {
                if (cntRight[i - 'a'] > 0 && cntLeft[i - 'a'] > 0) {
                    if (!seen[c - 'a'][i - 'a']) {
                        ++ans;
                        seen[c - 'a'][i - 'a'] = true;
                    }
                }
            }
            ++cntLeft[c - 'a'];
        }
        return ans;
    }
};
```

Time complexity: O(26*s.length),
space complexity: O(26 * 26).

## 1931. Painting a Grid With Three Different Colors

This can be considered an upgraded version of [1411. Number of Ways to Paint N × 3 Grid](https://leetcode.com/problems/number-of-ways-to-paint-n-3-grid/).

The number of rows changed from 3 to 1-5, but the idea is unchanged: still ternary bit_mask + DP.

Use a ternary bit_mask to represent the color state of each column, and derive the number of arrangements for the new column from the number of arrangements of the previous column.

```cpp
class Solution {
    const int MOD = 1e9 + 7;
public:
    int colorTheGrid(int m, int n) {
        int pow3m = 1;
        for (int x = 0; x < m; ++x) {
            pow3m *= 3;
        }
        auto ok = [&](int i, int j) -> bool {
            for (int x = 0; x < m; ++x) {
                if ((i % 3) == (j % 3)) return false;
                i /= 3;
                j /= 3;
            }
            return true;
        };
        auto isLegal = [&](int i) -> bool {
            int last = -1;
            for (int x = 0; x < m; ++x) {
                if (i % 3 == last) return false;
                last = i % 3;
                i /= 3;
            }
            return true;
        };
        
        vector<vector<int>> match(pow3m);
        vector<bool> legal(pow3m);
         for (int i = 0; i < (pow3m); ++i) {
              legal[i] = isLegal(i);
         }
         for (int i = 0; i < (pow3m); ++i) {
              // cout << legal[i] << " " << endl;
              if (!legal[i]) continue;
              for (int j = 0; j < (pow3m); ++j) {
                  // cout << i << " " << j << "....";
                  if (!legal[j]) continue;
                  // cout << i << " " << j << "****";
                  if (ok(i, j)) {
                      match[i].push_back(j);
                      // cout << i << " " << j << ";";
                  }
              }
         }
        vector<vector<int>> dp(pow3m, vector<int> (n, 0));
        for (int mask = 0; mask < (pow3m); ++mask) {
            if (legal[mask])
                dp[mask][0] = 1;
            // cout << dp[mask][0] << " ";
        }
        // cout << endl;
        for (int i = 1; i < n; ++i) {
            for (int mask = 0; mask < (pow3m); ++mask) {
                if (!legal[mask]) continue;
                for (int left : match[mask]) {
                    dp[mask][i] = (dp[mask][i] + dp[left][i-1]) % MOD;
                }
                // cout << dp[mask][i] << " ";
            }
            // cout << endl;
        }
        int ans = 0;
        for (int mask = 0; mask < (pow3m); ++mask) {
            if (legal[mask])
                ans = (ans + dp[mask][n-1]) % MOD;
        }
        return ans % MOD;
    }
};
```

Time complexity: O(3^m^2 + n*3^m),
space complexity: O(n * 3^m).

## 1932. Merge BSTs to Create Single BST

The algorithm is not difficult, but the implementation is rather complex, and corner cases are easy to fail.

Following the problem statement, find the correspondence between each root and leaf, then perform the merge operation.

Finally, it is still necessary to check whether the result is a BST.

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
    tuple<bool, int, int> solve(TreeNode* root) {
        // is BST, max value, min value
        if (!root) return {true, 0, 0};
        tuple<bool, int, int> ret = {true, root->val, root->val};
        if (root->left) {
            auto l = solve(root->left);
            get<0>(ret) = get<0>(ret) && get<0>(l) && root->val > get<1>(l);
            get<2>(ret) = min(get<2>(ret), get<2>(l));
        }
        if (root->right) {
            auto r = solve(root->right);
            get<0>(ret) = get<0>(ret) && get<0>(r) && root->val < get<2>(r);
            get<1>(ret) = max(get<1>(ret), get<1>(r));
        }
        return ret;
    }
    bool isValidBST(TreeNode* root) {
        return get<0>(solve(root));
    }
    int count(TreeNode* root) {
        if (!root) return 0;
        return count(root->left) + count(root->right) + 1;
    }
public:
    TreeNode* canMerge(vector<TreeNode*>& trees) {
        const int n = trees.size();
        unordered_map<TreeNode*, TreeNode*> leaves;
        unordered_map<int, TreeNode*> value2leaf;
        vector<TreeNode*> equalLeaves;
        bool bad = false;
        unordered_set<int> seen;
        function<void(TreeNode*, TreeNode*)> dfs = [&](TreeNode* root, TreeNode* parent) -> void {
            seen.insert(root->val);
            if (parent) {
                if (value2leaf.find(root->val) != value2leaf.end()) {
                    bad = true;
                    return;
                }
                leaves[root] = parent;
                value2leaf[root->val] = root;
            }
            if (root->left) {
                dfs(root->left, root);
            }
            if (root->right) {
                dfs(root->right, root);
            }
        };
        for (auto root : trees) {
            dfs(root, nullptr);
            if (bad) return nullptr;
        }
        TreeNode* ans = nullptr;
        for (auto root : trees) {
            auto it = value2leaf.find(root->val);
            if (it == value2leaf.end()) {
                if (ans != nullptr) return nullptr;
                ans = root;
            } else {
                auto leaf = it->second;
                auto parent = leaves[leaf];
                if (parent->left && parent->left->val == root->val) {
                    parent->left = root;
                } else if (parent->right && parent->right->val == root->val) {
                    parent->right = root;
                }
                
            }
        }
        if (ans) {
            if (!isValidBST(ans)) return nullptr;
            if (count(ans) != seen.size()) return nullptr;
        }
        return ans;
    }
};
```

Time complexity: O(trees.length),
space complexity: O(trees.length).
