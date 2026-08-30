---
title: LeetCode weekly contest 140
date: 2019-06-09 13:11:58
description: Weekly notes on a judging incident, adjacent-token state machines, tile permutations, recursive tree pruning and the smallest distinct subsequence.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/06/09/LeetCode-weekly-contest-140/
  en: https://youngforest.github.io/en/2019/06/09/LeetCode-weekly-contest-140/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-140/en-hero.webp" alt="A wordless algorithm workshop recalibrates a judging machine beside state beads, permutation tiles, a pruned tree and a monotonic rail" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (6) |	Q4 (8)|
|--|--|--|--|--|--|--|--|
| 313 / 4046 |	YoungForest | 16 | 1:03:21 | 0:21:32 (1) | 0:36:08 | 0:53:21 (1) | null |

This contest was moderately difficult. Because of [an issue with the judging program](https://leetcode.com/problems/insufficient-nodes-in-root-to-leaf-paths/discuss/308196/A-leaf-is-a-node-with-no-children.), many people were trapped by the third problem. The test case was corrected after the contest. This is already not the first LeetCode incident.

## 5083. Occurrences After Bigram

Idea:
A warm-up problem. Solve it directly. Use a state machine to record the current state.

Time complexity: O(text.size()),
Space complexity: O(1). In my implementation, I store tokens in a vector for convenience, but it is actually unnecessary.

```cpp
class Solution {
    enum class State {
        other,
        first,
        second
    };
public:
    vector<string> findOcurrences(string text, string first, string second) {
        vector<string> ret;
        istringstream iss(text);
        vector<string> tokens{istream_iterator<string>{iss}, {}};
        State state = State::other;
        for (const auto & token : tokens) {
            if (state == State::second) {
                ret.push_back(token);
                state = State::other;
            }
            if (token == first) {
                state = State::first;
            } else if (token == second && state == State::first) {
                state = State::second;
            } else {
                state = State::other;
            }
        }
        return ret;
    }
};
```

## 5087. Letter Tile Possibilities
Because the data scale is small, `tiles.length <= 7`, directly brute-force enumerate all possibilities.
Time complexity: O(N!),
Space complexity: O(N!).

```cpp
class Solution {
    set<string> ret;
    void backtracking(map<char, int>& count, int size, int step, string& current) {
        if (step == size) {
            ret.insert(current);
            return;
        }
        for (auto& p : count) {
            char c = p.first;
            if (count[c] > 0) {
                --count[c];
                current.push_back(c);
                backtracking(count, size, step + 1, current);
                current.pop_back();
                ++count[c];
            }
        } 
    }
public:
    int numTilePossibilities(string tiles) {
        map<char, int> count;
        for (char tile : tiles) {
            ++count[tile];
        }
        int s = tiles.size();
        for (int len = 1; len <= s; ++len) {
            string current;
            backtracking(count, len, 0, current);
        }
        // for (auto& s : ret) {
        //     cout << s << " ";
        // }
        return ret.size();
    }
};
```

## 5084. Insufficient Nodes in Root to Leaf Paths

A typical recursion problem. Just delete nodes according to the statement. If you are familiar with tree recursion, it is quick to write.
One thing to note: the definition of a leaf node is that both left and right subtrees are null, not just the left subtree or the right subtree.

Time complexity: O(N), each node in the tree calls the recursive function once.
Space complexity: O(N), the depth of the tree.

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
    int limit = 0;
    // return: 是否删除，子树从根到叶子最大的sum
    // current: 从根到父节点的sum
    pair<TreeNode*, int> recurse(TreeNode* root, int current) {
      if (root == nullptr) {
            return {nullptr, 0};
        }
        auto l = recurse(root->left, current + root->val);
        auto r = recurse(root->right, current + root->val);
        TreeNode* ret_ptr = nullptr;
        int ret_int;
        if (root->left != nullptr && root->right != nullptr)
            ret_int = max(l.second, r.second);
        else if (root->left != nullptr)
            ret_int = l.second;
        else if (root->right)
            ret_int = r.second;
        else    // 叶子结点
            ret_int = 0;
        root->left = l.first;
        root->right = r.first;
        // cout << root->val << " : " << ret_int << endl;
        if (ret_int + root->val + current >= limit) {
            return {root, ret_int + root->val};
        } else {
            return {nullptr, ret_int + root->val};
        }
    }
public:
    TreeNode* sufficientSubset(TreeNode* root, int limit) {
        this->limit = limit;
        auto ret = recurse(root, 0);
        return ret.first;
    }
};
```

## 5086. Smallest Subsequence of Distinct Characters

I did not solve this problem during the contest. I tried a greedy solution, adding one character each time. But this greedy idea is actually wrong and cannot handle input like
`"ddeeeccdce"`.
When processing the final `d`, `"ecd"` cannot be greater than `"dec"`.

```cpp
// 错误的贪心思路
// 时间复杂度: O(N^2)
// 空间复杂度: O(N)
class Solution {
public:
    string smallestSubsequence(string text) {
        string current;
        for (int i = 0; i < text.size(); ++i) {
            char c = text.at(i);
            auto index = current.find(c);
            if (index == string::npos) {
                current.push_back(c);
            } else {
                string new_current = current.substr(0, index) + current.substr(index + 1);
                new_current.push_back(c);
                if (new_current < current) {
                    current = std::move(new_current);
                }
            }
        }
        return current;
    }
};
```

Correct idea:
This is exactly the same problem as [LeetCode 316](https://leetcode.com/problems/remove-duplicate-letters).
There are many high-quality answers in the discuss section for that problem.

For the input string, we try to maintain a monotonically increasing result string. If the input character is smaller than the last character of the result string, and that last character will appear again later, then we remove that character from the result string.
This is also a greedy idea. Each operation makes the string smaller.

Time complexity: O(N),
Space complexity: O(N).

```cpp
class Solution {
public:
    string smallestSubsequence(string text) {
        unordered_map<char, int> used, count;
        for (char c : text) {
            ++count[c];
        }
        string ret;
        for (char c : text) {
            --count[c];
            if (used[c]++ > 0) {
                continue;
            }
            while (!ret.empty() && ret.back() > c && count[ret.back()] > 0) {
                used[ret.back()] = 0;
                ret.pop_back();
            }
            ret.push_back(c);
        }
        return ret;
    }
};
```
