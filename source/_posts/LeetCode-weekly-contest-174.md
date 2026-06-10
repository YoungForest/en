---
title: LeetCode weekly contest 174
date: 2020-02-03 20:18:32
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 459 / 6997 |	YoungForest | 	18	 | 	1:04:52 | 0:15:31 | 0:21:58 |  0:41:22  2 | 0:54:52 |

I finally returned to my dear motherland. I can get up on Sunday morning to play LeetCode contests again. I have to admit that after four months away, I became much rustier. The ranking says it all.
This contest was purely a speed contest, and speed depends heavily on form and familiarity. So I fully accept the drop in ranking. It also happens to push me to put more effort into preparing for the upcoming Google interview.

## 1341. The K Weakest Rows in a Matrix

Use binary search to find the number of soldiers in each row, then sort to find the first K rows with the fewest soldiers.

Time complexity: O(m log m * log n)
Space complexity: O(m)

```cpp
class Solution {
    bool decide(const vector<int>& row, int x) {
        return row[x] == 1;
    }
    int number(const vector<int>& row) {
        int lo = 0, hi = row.size();
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (decide(row, mid)) {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        return lo;
    }
public:
    vector<int> kWeakestRows(vector<vector<int>>& mat, int k) {
        vector<pair<int, int>> q;
        for (int i = 0; i < mat.size(); ++i) {
            q.push_back({number(mat[i]), i});
        }
        // for (auto a : q) {
        //     cout << a.first << " " << a.second << endl;
        // }
        sort(q.begin(), q.end(), [](auto a, auto b) -> bool {
            if (a.first == b.first) {
                return a.second < b.second;                
            } else {
                return a.first < b.first;
            }
        });
        // for (auto a : q) {
        //     cout << a.first << " " << a.second << endl;
        // }
        vector<int> ans;
        for (int i = 0; i < k; ++i) {
            ans.push_back(q[i].second);
        }
        return ans;
    }
};
```

## 1342. Reduce Array Size to The Half

Greedy strategy. Delete the number with the highest frequency each time.

Time complexity: O(N log N).
Space complexity: O(N).

```cpp
class Solution {
public:
    int minSetSize(vector<int>& arr) {
        unordered_map<int, int> count;
        for (int i : arr) {
            ++count[i];
        }
        vector<int> v;
        for (auto p : count) {
            v.push_back(p.second);
        }
        sort(v.begin(), v.end(), greater<int>());
        int ans = 0, target = (arr.size() + 1) / 2, accu = 0;
        while (accu < target) {
            accu += v[ans];
            ++ans;
        }
        return ans;
    }
};
```

## 1343. Maximum Product of Splitted Binary Tree

Enumerate and search directly. Try every subtree combination and find the maximum product.
Here we need to pay attention to the `modulo 10^9 + 7` operation. I got WA twice on this point. I used `unsigned long long`, and applied `mod` only at the end.

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
    using ll = unsigned long long;
    const ll mod = 1e9 + 7;
    ll ans = 0;
    ll sumOfSubtree(TreeNode* root, const ll total) {
        if (root == nullptr) {
            return 0;
        } else {
            ll l = sumOfSubtree(root->left, total);
            ll r = sumOfSubtree(root->right, total);
            if (root->left != nullptr) {
                ans = max(ans, l * (total - l));
            }
            if (root->right != nullptr) {
                ans = max(ans, r * (total - r));
            }
            return root->val + l + r;
        }
    }
    ll sum(TreeNode* root) {
        if (root) {
            return root->val + sum(root->left) + sum(root->right);
        } else {
            return 0;
        }
    }
public:
    int maxProduct(TreeNode* root) {
        ll total = sum(root);
        sumOfSubtree(root, total);
        return ans % mod;
    }
};
```

## 1344. Jump Game V

Top-down Dynamic Programming (DP). From each index, try jumping left and right until encountering a greater height.

Time complexity: O(N ^ 2).
Space complexity: O(N).

```cpp
class Solution {
    int dp(const vector<int>& arr, const int d, unordered_map<int, int>& memo, int x) {
        if (x < 0 || x >= arr.size()) {
            return 0;
        } else if (memo.find(x) != memo.end()) {
            return memo[x];
        }
        else {
            int ans = 1;
            for (int i = x + 1; i < arr.size() && i <= x + d && arr[i] < arr[x]; ++i) {
                ans = max(ans, dp(arr, d, memo, i) + 1);
            }
            for (int i = x - 1; i >= 0 && i >= x - d && arr[i] < arr[x]; --i) {
                ans = max(ans, dp(arr, d, memo, i) + 1);
            }
            return memo[x] = ans;
        }
    }
public:
    int maxJumps(vector<int>& arr, int d) {
        int ans = 0;
        unordered_map<int, int> memo;
        for (int i = 0; i < arr.size(); ++i) {
            ans = max(ans, dp(arr, d, memo, i));
        }
        return ans;
    }
};
```
