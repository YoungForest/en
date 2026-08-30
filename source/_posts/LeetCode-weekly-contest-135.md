---
title: LeetCode weekly contest 135
date: 2019-05-05 12:47:02
description: First top-two-hundred contest notes on collinearity, a greater-sum tree, polygon triangulation and moving stones into consecutive slots.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/05/05/LeetCode-weekly-contest-135/
  en: https://youngforest.github.io/en/2019/05/05/LeetCode-weekly-contest-135/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-135/en-hero.webp" alt="Forest reaches a blank finish arch beside a three-point frame, accumulating tree, triangulated polygon and consecutive stone track" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (5) |	Q4 (5)|
|--|--|--|--|--|--|--|--|
|70 / 3635	|	YoungForest |	15 | 1:34:07 | 0:07:28 | 0:16:45 |	null | 1:29:07  (1) |

This Sunday was a workday in China, so the number of people participating in LeetCode weekly contest directly dropped by 1,000, which shows how enthusiastic domestic participants are about this contest. Chinese contestants are also generally among the strongest in the world. So this time I ranked 70 and entered the top 200 for the first time. Besides the credit for racing against the clock and AC'ing the last problem before the end, there was also the reason that fewer strong contestants participated.

## 5051. Valid Boomerang

Intuition:
Divide it into two parts: checking distinctness and checking not in a straight line.
Three collinear points can be judged directly by slope.
Time complexity: O(1)
Space complexity: O(1)

```cpp
class Solution {
    bool isSame(vector<int>& x, vector<int>& y) {
        return x[0] == y[0] && x[1] == y[1];
    }
public:
    bool isBoomerang(vector<vector<int>>& points) {
        return !isSame(points[0], points[1]) && !isSame(points[1], points[2]) && !isSame(points[2], points[0]) && static_cast<double>(points[2][1] - points[1][1]) / static_cast<double>(points[2][0] - points[1][0]) != static_cast<double>(points[1][1] - points[0][1]) / static_cast<double>(points[1][0] - points[0][0]);
    }
};
```

Tricks found in discuss:
1. You can directly use whether the area is 0 to judge both distinctness and collinearity. The area formula is computed with cross product.
2. When judging slope, converting division to multiplication is better. Then there is no need to check distinctness in advance.

My solution has a division-by-zero problem,
but it can still pass test cases like `[[1,2],[1,1],[2,1]]`.
Thinking carefully, C++ floating-point division follows the IEEE 754 standard, so the result is +infinity. Naturally, the comparison produces a not-equal result.

## 1038. Binary Search Tree to Greater Sum Tree

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
    int current = 0;
    void recurse(TreeNode* root) {
        if (!root) return;
        recurse(root->right);
        current += root->val;
        root->val = current;
        recurse(root->left);
    }
public:
    TreeNode* bstToGst(TreeNode* root) {
        recurse(root);
        return root;
    }
};
```

## 1039. Minimum Score Triangulation of Polygon
[Reference](https://leetcode.com/problems/minimum-score-triangulation-of-polygon/discuss/286705/JavaC++Python-DP)

Intuition:
DP.
For an n-sided polygon, there can be a straight line that divides it into k-sided and n-k-sided polygons.
This line must be one side of a triangle.
Then enumerate the position of the other point and update the minimum value.

Time complexity: O(n ^ 3),
Space complexity: O(n ^ 2)

```cpp
class Solution {
public:
    int minScoreTriangulation(vector<int>& A) {
        const int n = A.size();
        vector<vector<int>> dp (n, vector<int> (n));
        for (int d = 2; d < n; ++d) { // i, j 之间的距离
            for (int i = 0; i + d< n; ++i) {   // 直线的一端
                int j = i + d;  // 直线的另一端
                dp[i][j] = numeric_limits<int>::max();
                for (int k = i + 1; k < j; ++k) { // 三角形的另一个顶点
                    dp[i][j] = min(dp[i][j], dp[i][k] + dp[k][j] + A[i] * A[k] * A[j]);
                }
            }
        }
        
        return dp[0][n-1];
    }
};
```

## 1040. Moving Stones Until Consecutive II

Time complexity: O(n log n), because there is a sort.
Space complexity: O(1)

```cpp
class Solution {
public:
    vector<int> numMovesStonesII(vector<int>& stones) {
        // 贪心策略
        // 最小：尽可能地收缩，答案一定小于size，连续的size内有多少石头，答案即为size-这个数目
        // 特殊情况：这个区间涉及到头或尾，且补位的位置也是头尾，即 1 11这种情况，此时答案为2，而不是1
        // 最大：先排个序，最小的跳到最大的左面，或 最大的额跳到最小的右面，剩下的每次收缩1
        sort(stones.begin(), stones.end());
        int size = stones.size();
        int left = 0;
        int minimum_moves = 0;
        for(int i = 0; i < size; ++i) {
            if (stones[i] >= stones[left] + size) {
                while (stones[i] >= stones[left] + size)
                    ++left;
            }
            minimum_moves = max(minimum_moves, i - left + 1);
        }
        if (minimum_moves == size - 1 && (stones[1] - stones[0] != 2 && stones[size - 1] - stones[size - 2] != 2)) {
            minimum_moves = 2;
        } else {
            minimum_moves = size - minimum_moves;
        }
        
        int maximum_moves = 0;
        maximum_moves = max(stones[size - 2] - stones[0], stones[size - 1] - stones[1]) + 1 - size + 1;
        
        return {minimum_moves, maximum_moves};
    }
};
```
