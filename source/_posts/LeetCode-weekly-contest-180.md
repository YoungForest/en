---
title: LeetCode Weekly Contest 180
date: 2020-03-15 22:04:28
description: Weekly notes on lucky matrix values, range-increment stacks, balanced search trees and team performance during a family visit.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/03/15/LeetCode-weekly-contest-180/
  en: https://youngforest.github.io/en/2020/03/15/LeetCode-weekly-contest-180/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-180/en-hero.webp" alt="Forest works beside a glowing bead matrix, lifted lower trays, a balanced branching mobile and runners coupled to an efficiency gear" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

Since LeetCode [updated the weekly contest rating algorithm](https://leetcode.com/discuss/general-discussion/518516/New-Rating-Algorithm-Details-Contest-Season-and-Absence-in-Participation), the result shocked me. My rating directly rose to 2171, with global rank 608/81184 and 53 contests completed. I remember that last week I was still hoping to break 2000 in the next few weeks, since I was already 1990+. The updated algorithm shows that I had already reached 2000 last August.

This Sunday I went back to the village to visit my grandmother. Because of the pandemic, the family had not been able to get together for a while. Today, at long last, almost everyone was there. I also joined the weekly contest from my hometown. Because the environment was not suitable for thinking, the result was only so-so.

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (4) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 1300 / 10047 |	YoungForest | 	11	 | 0:29:31 | 0:12:20 | 0:18:26 | 0:29:31 | null |

## 1380. Lucky Numbers in a Matrix

Record the column index of the minimum value in each row and the row index of the maximum value in each column, then check whether any pair matches.

Time complexity: O(m * n),
space complexity: O(m + n).

```cpp
class Solution {
public:
    vector<int> luckyNumbers (vector<vector<int>>& matrix) {
        const int m =  matrix.size();
        if (m == 0) return {};
        const  int n = matrix[0].size();
        vector<int> row_min_index(m, -1);
        vector<int> col_max_index(n, -1);
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (row_min_index[i] == -1 || matrix[i][row_min_index[i]] > matrix[i][j]) {
                    row_min_index[i] = j;
                }
                if (col_max_index[j] == -1 || matrix[col_max_index[j]][j] < matrix[i][j]) {
                    col_max_index[j] = i;
                }
            }
        }
        vector<int> ans;
        for (int row = 0; row < m; ++row) {
            if (col_max_index[row_min_index[row]] == row) {
                ans.push_back(matrix[row][row_min_index[row]]);
            }
        }
        return ans;
    }
};
```

## 1381. Design a Stack With Increment Operation

Just implement the stack with an array.

Time complexity:
- Constructor: O(1)
- push: O(1)
- pop: O(1)
- increment: O(k)
Space complexity: O(maxSize)

There is also a [Lazy Increment algorithm](https://leetcode.com/problems/design-a-stack-with-increment-operation/discuss/539716/JavaC++Python-Lazy-increment-O(1)). During `increment`, it only records a mark, and the actual increment happens when `pop` is called. This can make `increment` O(1).

```cpp
class CustomStack {
    vector<int> data;
    int index;
    int max_size;
public:
    CustomStack(int maxSize) {
        data.resize(maxSize);
        index = 0;
        max_size = maxSize;
    }
    
    void push(int x) {
        if (index < max_size) {
            data[index++] = x;
        }
    }
    
    int pop() {
        if (index > 0) {
            return data[--index];
        } else {
            return -1;
        }
    }
    
    void increment(int k, int val) {
        for (int i = 0; i < k && i < index; ++i) {
            data[i] += val;
        }
    }
};

/**
 * Your CustomStack object will be instantiated and called as such:
 * CustomStack* obj = new CustomStack(maxSize);
 * obj->push(x);
 * int param_2 = obj->pop();
 * obj->increment(k,val);
 */
```

## 1382. Balance a Binary Search Tree

First use inorder traversal to sort the data, then rebuild the tree using binary partitioning.

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
public:
    TreeNode* balanceBST(TreeNode* root) {
        if (!root) return nullptr;
        vector<int> element;
        function<void(TreeNode*)> store = [&](TreeNode* root) -> void {
            if (!root) {
                return;
            } else {
                store(root->left);
                element.push_back(root->val);
                store(root->right);
            }
        };
        store(root);
        function<TreeNode*(int, int)> build = [&](int begin, int end) -> TreeNode* {
            if (begin > end) return nullptr;
            else if (begin == end) return new TreeNode(element[begin]);
            else {
                int mid = begin + (end - begin) / 2;
                auto ret = new TreeNode(element[mid]);
                ret->left = build(begin, mid - 1);
                ret->right = build(mid + 1, end);
                return ret;
            }
        };
        return build(0, element.size() - 1);
    }
};
```

## 1383. Maximum Performance of a Team

A greedy algorithm. First sort by `efficiency` from high to low, pick the `k` people with the highest efficiency, then traverse people with slightly lower efficiency, trying to add them to the team by removing the one with the smallest `speed`, and update the possible maximum performance.

Time complexity: O(N log N + N log K),
space complexity: O(N + K).

Similar problem: [LeetCode 857](https://leetcode.com/problems/minimum-cost-to-hire-k-workers/description/).

```cpp
class Solution {
    using ll = long long;
    const ll MOD = 1e9 + 7;
    const ll INF = 0x3f3f3f3f;
public:
    int maxPerformance(int n, vector<int>& speed, vector<int>& efficiency, int k) {
        ll ans = 0;
        priority_queue<int, vector<int>, greater<int>> pq;
        vector<pair<int, int>> workers;
        for (int i = 0; i < n; ++i) {
            workers.push_back({efficiency[i], speed[i]});
        }
        sort(workers.begin(), workers.end(), greater<pair<int, int>>());
        ll sum_speed = 0;
        ll min_efficiency = INF;
        for (int i = 0; i < k; ++i) {
            sum_speed += workers[i].second;
            min_efficiency = workers[i].first;
            pq.push(workers[i].second);
            ans = max(sum_speed * min_efficiency, ans);
        }
        for (int i = k; i < n; ++i) {
            min_efficiency = workers[i].first;
            if (workers[i].second > pq.top()) {
                auto t = pq.top();
                pq.pop();
                sum_speed = sum_speed - t + workers[i].second;
                pq.push(workers[i].second);
                ans = max(ans, sum_speed * min_efficiency);
            }
        }
        return ans % MOD;
    }
};
```
