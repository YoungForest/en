---
title: LeetCode weekly contest 145
date: 2019-07-15 15:08:44
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/07/15/LeetCode-weekly-contest-145/
  en: https://youngforest.github.io/en/2019/07/15/LeetCode-weekly-contest-145/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (5) |	Q2 (5) |	Q3 (8) |	Q4 (8)|
|--|--|--|--|--|--|--|--|
| 451 / 4931 |	YoungForest | 16 | 1:24:26 | 0:09:37 | 0:17:39 | 1:14:26 2 | null |

## 1122. Relative Sort Array

Custom sorting rule.

Time complexity: O(N log N),
Space complexity: O(N).

```cpp
class Solution {
public:
    vector<int> relativeSortArray(vector<int>& arr1, vector<int>& arr2) {
        unordered_map<int, int> location;
        for (int i = 0; i < arr2.size(); ++i) {
            location[arr2[i]] = i;
        }
        sort(arr1.begin(), arr1.end(), [location](int lhs, int rhs) -> bool {
            if (location.find(lhs) != location.end() && location.find(rhs) != location.end()) {
                return location.at(lhs) < location.at(rhs);
            } else if (location.find(lhs) == location.end() && location.find(rhs) != location.end()) {
                return false;
            } else if (location.find(lhs) != location.end() && location.find(rhs) == location.end()) {
                return true;
            } else {
                return lhs < rhs;
            }
        });
        return arr1;
    }
};
```

## 1123. Lowest Common Ancestor of Deepest Leaves

Tree problems are solved recursively. Focus on what information needs to be returned to the parent node and what information needs to be passed to child nodes.

Time complexity: O(N). Each node is traversed once.
Space complexity: O(N). The maximum depth of the tree, which is the recursion depth.

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
    pair<int, TreeNode*> recurse(TreeNode* root, int depth) {
        if (root == nullptr) {
            return {depth, nullptr};
        }
        auto l = recurse(root->left, depth + 1);
        auto r = recurse(root->right, depth + 1);
        if (l.first > r.first) {
            return l;
        } else if (l.first == r.first) {
            return {l.first, root};
        } else {
            return r;
        }
    }
public:
    TreeNode* lcaDeepestLeaves(TreeNode* root) {
        auto ans = recurse(root, 0);
        return ans.second;
    }
};
```

## 1124. Longest Well-Performing Interval

We only care whether each element is greater than 8, not its absolute value. So transform elements greater than 8 into 1, and elements less than or equal to 8 into 0. The problem becomes finding the longest subarray where the number of 1s is greater than the number of 0s.

To quickly calculate how many more 1s than 0s a subarray has, we can use prefix sums.

For example:

[9,9,6,0,6,6,9] ->
[1,1,0,0,0,0,1] -> prefix sum
[0,1,2,1,0,-1,-2,-3]

The number of 1s minus 0s in subarray `[i, j]` is `prefix[j] - prefix[i - 1]`, and the array length is `j - i + 1`.

That is, for each `j`, we want to find the earliest `prefix[i-1]` smaller than `prefix[j]`. Because prefix changes continuously, finding the one exactly smaller than `prefix[j]` is earlier. For `prefix[j] > 0`, just find 0.

Another way to find the earliest `prefix[i-1]` smaller than `prefix[j]` is to maintain a decreasing stack recording `prefix[i], i`, then use binary search to find the appropriate `prefix[i-1]`.

Great solution: [tiankonguse](https://leetcode.com/problems/longest-well-performing-interval/discuss/334897/ChineseC%2B%2B-1124.-O(n))

[Huahua's video explanation](https://www.bilibili.com/video/av59225187)

```cpp
class Solution {
public:
    int longestWPI(vector<int>& hours) {
        unordered_map<int, int> memo; // sum, index
        int s = 0;
        memo[0] = -1;
        int ret = 0;
        for (int i = 0; i < hours.size(); ++i) {
            if (hours[i] > 8) {
                ++s;
            } else {
                --s;
            }
            if (memo.find(s) == memo.end()) {
                memo[s] = i;
            }
            if (s - 1 <= 0 && memo.find(s - 1) != memo.end()) {
                ret = max(ret, i - memo[s - 1]);
            }
            if (s - 1 > 0) {
                ret = max(ret, i - memo[0]);
            }
        }
        return ret;
    }
};
```

## 1125. Smallest Sufficient Team

[DP + bitmask](https://leetcode.com/problems/smallest-sufficient-team/discuss/334832/c%2B%2B-dp-bitmask-solution-with-algorithm)

Since this problem is an NP problem.

Time complexity: O(2^(req_skills.size()) * people.size() * people[i].size())
Space complexity: O(2^(req_skills.size()))

```cpp
class Solution {
public:
    vector<int> smallestSufficientTeam(vector<string>& req_skills, vector<vector<string>>& people) {
        const int n = req_skills.size();
        unordered_map<string, int> skill2bit;
        for (int i = 0; i < req_skills.size(); ++i) {
            skill2bit[req_skills[i]] = 1 << i;
        }
        map<int, vector<int>> res;
        res[0] = {};
        for (int i = 0; i < people.size(); ++i) {
            const auto& p = people[i];
            int p_skill = 0;
            for (const auto& skill : p) {
                p_skill |= skill2bit[skill];
            }
            for (auto it = res.begin(); it != res.end(); ++it) {
                int combine = p_skill | it->first;
                auto comb_it = res.find(combine);
                if (comb_it == res.end() || it->second.size() + 1 < comb_it->second.size()) {
                    res[combine] = it->second;
                    res[combine].push_back(i);
                }
            }
        }
        return res[(1 << n) - 1];
    }
};
```
