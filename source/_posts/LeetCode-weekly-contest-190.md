---
title: LeetCode Weekly Contest 190
date: 2020-05-24 12:43:24
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/05/24/LeetCode-weekly-contest-190/
  en: https://youngforest.github.io/en/2020/05/24/LeetCode-weekly-contest-190/
---
Last night, my dad helped me clean my ear, and accidentally made it bleed. I went to the district hospital early this morning for a checkup. Fortunately, it was nothing serious; only the external ear canal was injured. Rest for a week and it should heal naturally. As long as it does not get infected, it is fine. I was prescribed some amoxicillin.
So I skipped the weekly contest and solved the problems after the contest.

## 1455. Check If a Word Occurs As a Prefix of Any Word in a Sentence

C++ does not have a built-in method for splitting strings, but we can use our own template. Implement the split through `stringstream`, with O(N) complexity.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
    vector<string> splitSentence(const string& text) {
        string tmp;
        vector<string> stk;
        stringstream ss(text);
        while(getline(ss,tmp,' ')) {
            stk.push_back(tmp);
        }
        return stk;
    }
public:
    int isPrefixOfWord(string sentence, string searchWord) {
        auto words = splitSentence(sentence);
        for (int i = 0; i < words.size(); ++i) {
            if (words[i].find(searchWord) == 0) return i + 1;
        }
        return -1;
    }
};
```

## 1456. Maximum Number of Vowels in a Substring of Given Length

Sliding window. The window size is `k`, and we count the vowels inside the window.

Time complexity: O(N),
space complexity: O(1).

```cpp
class Solution {
public:
    int maxVowels(string s, int k) {
        int count = 0;
        unordered_set<int> vowels = {
            'a', 'e', 'i', 'o', 'u'
        };
        for (int i = 0; i < k; ++i) {
            if (vowels.find(s[i]) != vowels.end()) {
                ++count;
            }
        }
        int ans = count;
        for (int i = k; i < s.size(); ++i) {
            if (vowels.find(s[i]) != vowels.end()) {
                ++count;
            }
            if (vowels.find(s[i-k]) != vowels.end()) {
                --count;
            }
            ans = max(ans, count);
        }
        return ans;
    }
};
```

## 1457. Pseudo-Palindromic Paths in a Binary Tree

Determine whether a path can form a palindrome by checking whether the number of letters with odd frequency is <= 1.
Use DFS to search each path.

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
    bool isPalindromicPath(const unordered_map<int, int>& count) {
        int odd = 0;
        for (const auto& p : count) {
            if (p.second % 2 == 1) {
                ++odd;
            }
        }
        return odd <= 1;
    }
public:
    int pseudoPalindromicPaths (TreeNode* root) {
        unordered_map<int, int> path;
        int ans = 0;
        function<void(TreeNode*)> dfs = [&](TreeNode* root) -> void {
            if (root) {
                ++path[root->val];
                if (!root->left && !root->right) { // leaf
                    if (isPalindromicPath(path)) {
                        ++ans;
                    }
                }
                dfs(root->left);
                dfs(root->right);
                --path[root->val];
            }
        };
        dfs(root);
        return ans;
    }
};
```

## 1458. Max Dot Product of Two Subsequences

DP. `dp[i][j]` represents the max dot product of `nums1[:i+1]` and `nums2[:j+1]`.

`dp[i][j] = max({dp[i-1][j], dp[i][j-1], dp[i-1][j-1] + nums1[i]*nums2[j], nums1[i]*nums2[j]});`

One thing to note is that an empty subsequence is not allowed.

Time complexity: O(nums1.size() * nums2.size()),
space complexity: O(nums1.size() * nums2.size()).

```cpp
class Solution {
public:
    int maxDotProduct(vector<int>& nums1, vector<int>& nums2) {
        const int m = nums1.size();
        const int n = nums2.size();
        vector<vector<int>> dp(m, vector<int>(n, 0));
        // nums[i], nums[j], max dot product
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (i > 0 && j > 0) {
                    dp[i][j] = max({dp[i-1][j], dp[i][j-1], dp[i-1][j-1] + nums1[i]*nums2[j], nums1[i]*nums2[j]});
                } else if (i == 0 && j == 0) {
                    dp[i][j] = nums1[i]*nums2[j];
                } else if (i == 0) {
                    dp[i][j] = max({dp[i][j-1], nums1[i]*nums2[j]});
                } else {
                    dp[i][j] = max({dp[i-1][j], nums1[i]*nums2[j]});
                }
            }
        }
        return dp[m-1][n-1];
    }
};
```
