---
title: LeetCode weekly contest 148
date: 2019-08-04 12:03:11
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (6) |	Q4 (8)|
|--|--|--|--|--|--|--|--|
| 77 / 5319 |	YoungForest | 23	 | 		0:56:45 | 0:09:51  | 0:24:02 | 0:41:20 | 0:56:45 |

This contest was my first time entering the top 100 again after three months, and it was also my second consecutive top-200 finish, so I was a little happy. It swept away the gloom from last week's Kick Start failure.
In fact, because of [a judge issue](https://leetcode.com/problems/longest-chunked-palindrome-decomposition/discuss/350633/Wrong-problem-constraints-rejudge-all-wrong-submissions), the rank I saw right after the contest ended was 56. Later, the official side rejudged the last problem, so some people's submissions could pass, and there was no penalty time.
This was also a pretty high-quality contest. The problems were not very hard, but they covered knowledge points quite comprehensively, and the difficulty distribution was reasonable.

## 1144. Decrease Elements To Make Array Zigzag

Because you can only decrease by 1, this is an Easy problem. If the operation were changed to +1 or -1, it would be much harder.
You only need to compare the two cases and choose the smaller one.

Time complexity: O(N),
Space complexity: O(1).

```cpp
class Solution {
    const int INF = 0x3f3f3f3f;
public:
    int movesToMakeZigzag(vector<int>& nums) {
        int ans1 = 0;
        for (int i = 0; i < nums.size(); i += 2) {
            int neighbor = INF;
            if (i > 0) {
                neighbor = min(neighbor, nums[i - 1]);
            }
            if (i < nums.size() - 1) {
                neighbor = min(neighbor, nums[i + 1]);
            }
            if (neighbor != INF && neighbor <= nums[i]) {
                ans1 += nums[i] - neighbor + 1;
            }
        }
        int ans2 = 0;
        for (int i = 1; i < nums.size(); i += 2) {
            int neighbor = INF;
            if (i > 0) {
                neighbor = min(neighbor, nums[i - 1]);
            }
            if (i < nums.size() - 1) {
                neighbor = min(neighbor, nums[i + 1]);
            }
            if (neighbor != INF && neighbor <= nums[i]) {
                ans2 += nums[i] - neighbor + 1;
            }
        }
        return min(ans1, ans2);
    }
};
```

## 1145. Binary Tree Coloring Game

The hard part of this problem is understanding the statement. The idea itself is very intuitive: choose the largest branch whenever possible. If the size of the large branch is greater than half, then it is a guaranteed win.
For tree problems, use recursion. The template is still DFS.

Time complexity: O(N), traverse each node once;
Space complexity: O(N). The depth of the tree, N in the worst case.

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
    int l, r;
    // return the number of node in subtree
    int dfs(TreeNode* root, int x) {
        if (root == nullptr)
            return 0;
        int left = dfs(root->left, x);
        int right = dfs(root->right, x);
        if (root->val == x) {
            l = left;
            r = right;
        }
        return left + right + 1;
    }
public:
    bool btreeGameWinningMove(TreeNode* root, int n, int x) {
        dfs(root, x);
        if (n == l + r + 1) {
            return l != r;
        } else {
            int parent = n - l - r - 1;
            return parent - 1 > l + r || l - 1 > r + parent || r - 1 > l + parent;
        }
    }
};
```

## 1146. Snapshot Array

The brute-force idea is to save all state every time `snap` is called. That makes the time and space cost of `snap` quite large.
Another clever idea, which is not too hard to come up with, is to save only the changed content each time. This is the solution I used.

Time complexity:
- SnapshotArray: O(N)
- set: O(1)
- snap:  O(1)
- get: O(log N)

```cpp
class SnapshotArray {
    vector<vector<pair<int, int>>> data;
    int current_snap_id = 0;
    const int INF = numeric_limits<int>::max();
public:
    SnapshotArray(int length) {
        data.resize(length, vector<pair<int, int>>(1, {0, 0}));
    }
    
    void set(int index, int val) {
        data[index].push_back({current_snap_id, val});
    }
    
    int snap() {
        return current_snap_id++;
    }
    
    int get(int index, int snap_id) {
        auto& target = data[index];
        auto it = lower_bound(target.begin(), target.end(), make_pair(snap_id, INF));
        --it;
        return it->second;
    }
};

/**
 * Your SnapshotArray object will be instantiated and called as such:
 * SnapshotArray* obj = new SnapshotArray(length);
 * obj->set(index,val);
 * int param_2 = obj->snap();
 * int param_3 = obj->get(index,snap_id);
 */
```

## 1147. Longest Chunked Palindrome Decomposition

Greedy. Each time, greedily look for equal head and tail chunks.
Time complexity: O(n ^ 2),
Space complexity: O(N).

```cpp
class Solution {
    bool equal(const string& text, int begin1, int end1, int begin2, int end2) {
        if (end1 - begin1 != end2 - begin2) {
            return false;
        }
        for (int i = 0; i < end1 - begin1; ++i) {
            if (text[begin1 + i] != text[begin2 + i]) 
                return false;
        }
        return true;
    }
    int recurse(const string& text, int begin, int end) {
        if (begin == end)
            return 0;
        for (int i = 1; begin + i <= end - i; ++i) {
            if (equal(text, begin, begin + i, end - i, end)) {
                return recurse(text, begin + i, end - i) + 2;
            }
        }
        return 1;
    }
public:
    int longestDecomposition(string text) {
        return recurse(text, 0, text.size());
    }
};
```
