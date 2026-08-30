---
title: LeetCode weekly contest 144
date: 2019-07-07 19:18:15
description: Post-exam notes on address replacement, difference-style flight bookings, deleting tree nodes and splitting nested parentheses.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/07/07/LeetCode-weekly-contest-144/
  en: https://youngforest.github.io/en/2019/07/07/LeetCode-weekly-contest-144/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-144/en-hero.webp" alt="Forest leaves a blank exam room for a protected address chain, flight-change rail, split tree and two nesting tracks" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

This morning I had to take the TOEFL exam, so I could not participate in the weekly contest as usual. I solved the problems after the contest.


## 1108. Defanging an IP Address
One pass. Just replace directly.

Time complexity: O(N),
Space complexity: O(1).

```cpp
class Solution {
public:
    string defangIPaddr(string address) {
        string ret;
        for (char c : address) {
            if (c == '.') {
                ret.push_back('[');
                ret.push_back('.');
                ret.push_back(']');
            } else {
                ret.push_back(c);
            }
        }
        return ret;
    }
};
```

## 1109. Corporate Flight Bookings

Brute force. TLE.
Time complexity: O(N * bookings.length);
Space complexity: O(N).

```cpp
class Solution {
public:
    vector<int> corpFlightBookings(vector<vector<int>>& bookings, int n) {
        vector<int> ret(n, 0);
        for (const auto& booking : bookings) {
            for (int i = booking.at(0); i <= booking.at(1); ++i) {
                ret[i - 1] += booking.at(2);
            }
        }
        return ret;
    }
};
```

One pass. I had just done a similar problem a few days earlier: passengers getting on and off at stops.
Time complexity: O(max(booking.length, N)).
Space complexity: O(N).

```cpp
class Solution {
public:
    vector<int> corpFlightBookings(vector<vector<int>>& bookings, int n) {
        vector<int> stops(n + 1, 0);
        vector<int> ret(n, 0);
        for (const auto& booking : bookings) {
            stops[booking[0] - 1] += booking[2];    // begin
            stops[booking[1]] -= booking[2];    // end
        }
        int total = 0;
        for (int i = 0; i < n; ++i) {
            total += stops[i];
            ret[i] = total;
        }
        return ret;
    }
};
```

## 1110. Delete Nodes And Return Forest

The general solution for binary trees: recursive deletion.

Time complexity: O(N), each node is recursively called once.
Space complexity: O(N). In the worst case, this is the number of returned subtrees.

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
    vector<TreeNode*> ret;
    TreeNode* recurse(TreeNode* root, const unordered_set<int>& to_delete) {
        if (root == nullptr)
            return nullptr;
        TreeNode* ret = nullptr;
        root->left = recurse(root->left, to_delete);
        root->right = recurse(root->right, to_delete);
        if (to_delete.find(root->val) == to_delete.end()) {
            ret = root;
        } else {    // delete
            if (root->left) {
                this->ret.push_back(root->left);
            }
            if (root->right) {
                this->ret.push_back(root->right);
            }
        }
        return ret;
    }
public:
    vector<TreeNode*> delNodes(TreeNode* root, vector<int>& to_delete) {
        unordered_set<int> td(to_delete.begin(), to_delete.end());
        auto r = recurse(root, td);
        if (r) {
            ret.push_back(r);
        }
        return ret;
    }
};
```

## 1111. Maximum Nesting Depth of Two Valid Parentheses Strings

When understanding the statement, note that A and B are subsequences of `seq`, not subarrays.
First try a greedy idea. When encountering a left parenthesis, assign it to the sequence with smaller depth. When encountering a right parenthesis, assign it to the sequence with larger depth.

Time complexity: O(N),
Space complexity: O(N).

```cpp
class Solution {
public:
    vector<int> maxDepthAfterSplit(string seq) {
        int a = 0, b = 0;
        vector<int> ret;
        for (char c : seq) {
            if (c == '(') {
                if (a <= b) {
                    ++a;
                    ret.push_back(0);
                } else {
                    ++b;
                    ret.push_back(1);
                }
            } else {
                if (a >= b) {
                    --a;
                    ret.push_back(0);
                } else {
                    --b;
                    ret.push_back(1);
                }
            }
        }
        return ret;
    }
};
```
