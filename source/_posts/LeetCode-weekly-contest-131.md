---
title: LeetCode weekly contest 131
date: 2019-04-07 12:19:41
tags:
- Competitive Programming
categories:
- LeetCode
---

The problem numbers in this contest startled me. LeetCode is really willful, directly starting problem numbers from 5000+. It looks like the room for growth in problem count has already exceeded my imagination.

Back to the point, this contest was also mainly about speed on simple problems.
| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (5) |	Q4 (5)|
|--|--|--|--|--|--|--|--|
|323 / 4894|	YoungForest |	22 | 	0:59:58	 | 0:10:44 | 0:19:06(2) |	0:30:57 |0:49:58 |
It also roughly required finishing within 50 minutes to enter the top 200.

## 1021. Remove Outermost Parentheses

Intution:
A parentheses matching problem. Use stack thinking and set a flag to indicate whether it is Outermost. Use state-machine thinking to construct the returned string.

Time complexity: O(N)
Space complexity: O(1)

```cpp
class Solution {
public:
    string removeOuterParentheses(string S) {
        int flag = 0;
        string ret;
        for (char c : S) {
            if (c == '(') {
                if (flag != 0)
                    ret.push_back(c);
                flag++;
            } else if (c == ')') {
                if (flag != 1)
                    ret.push_back(c);
                flag--;
            }
        }
        
        return ret;
    }
};
```

## 1022. Sum of Root To Leaf Binary Numbers

Intution:
Recurse, passing the binary-represented value by value.

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
    const static int mod = 1e9 + 7;
    int ret = 0;
    void dfs(TreeNode* root, int value) {
        int current_value = (value << 1) % mod + root->val;
        if (root->left) dfs(root->left, current_value);
        if (root->right) dfs(root->right, current_value);
        if (!root->left && !root->right)
            ret = (ret + current_value) % mod;
    }
public:
    int sumRootToLeaf(TreeNode* root) {
        dfs(root, 0);
        return ret;
    }
};
```

Note that there are two places to take modulo:
- Calculating `current_value`
- Accumulating `ret`

Time complexity: O(N), all nodes are traversed once.
Space complexity: O(N), the depth of the tree, which is equal to the number of nodes in the worst case.

## 1023. Camelcase Matching

Intution:
Compare directly. Borrow the state-machine idea: under different conditions, perform different operations (changes to `i` and `j`).

```cpp
class Solution {
    bool answer(const string& query, const string& pattern) {
        int i, j;
        for (i = 0, j = 0; i < pattern.size() && j < query.size();) {
            if (query[j] == pattern[i]) {
                i++;
                j++;
            } else if (islower(query[j])) {
                j++;
            } else {
                return false;
            }
        }
        if (i == pattern.size()) {
            for (; j < query.size(); j++) {
                if (!islower(query[j]))
                    return false;
            }
        } else {
            return false;
        }
        
        return true;
    }
public:
    vector<bool> camelMatch(vector<string>& queries, string pattern) {
        vector<bool> ret;
        for (auto query : queries) {
            ret.push_back(answer(query, pattern));
        }
        return ret;
    }
};
```

Time complexity: O(N * M), the length of `queries` and the length of each `query`.
Space complexity: O(N).

## 1024. Video Stitching

Intution:
Greedy. Each added clip makes the stitched video as long as possible.

```cpp
class Solution {
public:
    int videoStitching(vector<vector<int>>& clips, int T) {
        // greedy
        int ret = 0;
        int current_interval_right = 0;
        for (;current_interval_right < T;) {
            int max_left = 0;
            for (const auto& clip : clips) {
                if (clip[0] <= current_interval_right)
                    max_left = max(clip[1], max_left);
            }
            if (current_interval_right == max_left) {
                return -1;
            }
            current_interval_right = max_left;
            ret ++;
        }
        return ret;
    }
};
```

Time complexity: O(N ^ 2).
Space complexity: O(1).

## Afterword

Recently I took Microsoft's summer intern written test. I passed the warm-up problem, but one case in the third problem timed out. Later, after searching online, I learned there is a data structure called an order statistic tree, which can implement rank, deleting arbitrary nodes, and deleting the head node in O(log n).
The difficulty of Microsoft and Google's written tests has risen to ICPC level (it cannot be called ACM now, because ACM no longer sponsors it. Maybe it should be called IBM-ICPC now. Haha). By comparison, LeetCode is only the little brother.
This summer, I should make time to study the algorithm contest books I bought earlier. It will definitely help a lot.
*Programming Pearls* (even a copy personally signed by Zou Xin) has been in my hands for 3 years, yet I still have not finished it.

If I had to name the things I regret most from college: first, not figuring out earlier what I wanted to do after graduation; second, not holding tightly onto tls's leg and getting started with ACM.

One meaningful thing I did this week was signing up for Google Summer of Code. Although the chance of being selected is not high, I can try this year first and lay the groundwork for applying again next year.

