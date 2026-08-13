---
title: LeetCode Biweekly Contest 21
date: 2020-03-08 11:35:48
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/03/08/LeetCode-biweekly-contest-21/
  en: https://youngforest.github.io/en/2020/03/08/LeetCode-biweekly-contest-21/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 175 / 4729 |	YoungForest | 19 | 	1:05:07 | 0:21:38 |  0:39:43 |  0:50:40 |  1:05:07 |

Overall, the difficulty was not high, especially the last two problems, which were not as hard as they should have been.

## 1370. Increasing Decreasing String

Directly simulate the process of constructing the result string. The process of searching in the string can use binary search. Since the original string needs to be updated, a binary search tree data structure is better.

Time complexity: O(N * log N),
space complexity: O(N).

```cpp
class Solution {
public:
    string sortString(string s) {
        string ans;
        multiset<char> container;
        for (char c : s) {
            container.insert(c);
        }
        while (!container.empty()) {
            char smallest = *container.begin();
            container.erase(container.begin());
            ans.push_back(smallest);
            decltype(container.begin()) it;
            while ((it = container.upper_bound(smallest)) != container.end()) {
                ans.push_back(*it);
                smallest = *it;
                container.erase(it);
            }
            if (!container.empty()) {
                char largest = *(prev(container.end()));
                container.erase(prev(container.end()));
                ans.push_back(largest);
                while ((it = container.lower_bound(largest)) != container.begin()) {
                    ans.push_back(*prev(it));
                    largest = *prev(it);
                    container.erase(prev(it));
                }
            }
        }
        return ans;
    }
};
```

## 1371. Find the Longest Substring Containing Vowels in Even Counts

Observation: odd minus odd equals even, and even minus even equals even. So the problem can be transformed into finding the leftmost position with a given odd/even count state. Since there are 5 vowels and each has odd/even parity, there are 32 states in total.

Time complexity: O(N),
space complexity: O(1).

```cpp
class Solution {
public:
    int findTheLongestSubstring(string s) {
        vector<int> left_state(32, -2);
        left_state[0] = -1;
        auto code = [&](unordered_map<char, int>& m) -> unsigned int {
            const static vector<char> position = {'a', 'e', 'i', 'o', 'u'};
            unsigned  int ans = 0;
            for (unsigned  int i = 0; i < position.size(); ++i) {
                if  (m[position[i]] % 2 == 1) {
                    ans |= (1 << i);
                }
            }
            return ans;
        };
        unordered_map<char, int> m;
        int ans = 0;
        for (int i = 0; i < s.size(); ++i) {
            ++m[s[i]];
            auto c = code(m);
            if (left_state[c] != -2) {
                ans = max(ans, i - left_state[c]);
            } else {
                left_state[c] = i;
            }
        }
        return ans;
    }
};
```

## 1372. Longest ZigZag Path in a Binary Tree

Tree problems can be solved with recursion.

Time complexity: O(N),
space complexity: O(N).

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
    // left, right
    // return result if root is left/right child
    int ret = 0;
    pair<int, int> recurse(TreeNode* root) {
        if (root) {
            auto l = recurse(root->left);
            auto r = recurse(root->right);
            ret = max({ret, r.second + 1, l.first + 1});
            return {r.second + 1, l.first + 1};
        } else {
            return {-1, -1};
        }
    }
public:
    int longestZigZag(TreeNode* root) {
        auto ans = recurse(root);
        return ret;
    }
};
```

## 1373. Maximum Sum BST in Binary Tree

Still, tree problems can be solved with recursion.
The return values are, in order:
- the minimum value in the subtree
- the maximum value in the subtree
- whether it is a BST
- the sum of the subtree

Time complexity: O(N),
space complexity: O(height).

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
    int ret = 0;
    // smallest, largest, BST?, sum
    tuple<int, int, bool, int> recurse(TreeNode* root) {
        if (!root) {
            return {0, 0, true, 0};
        } else {
            auto l = recurse(root->left);
            auto r = recurse(root->right);
            bool isBST = (root->left == nullptr || get<1>(l) < root->val) && (root->right == nullptr || get<0>(r) > root->val) && get<2>(l) && get<2>(r);
            int smallest = root->val, largest = root->val;
            if (root->left) {
                smallest = min(smallest, get<0>(l));
                largest = max(largest, get<1>(l));
            }
            if (root->right) {
                smallest = min(smallest, get<0>(r));
                largest = max(largest, get<1>(r));
            }
                   
            int s = get<3>(l) + get<3>(r) + root->val;
            if (isBST)
                ret = max(s, ret);
            return {smallest, largest, isBST, s};
        }
    }
public:
    int maxSumBST(TreeNode* root) {
        recurse(root);
        return ret;
    }
};
```
