---
title: LeetCode 2021 Spring Contest - Team
date: 2021-04-11 21:53:03
description: "The recap balances bucket upgrades against water-filling rounds, then distributes a bounded connected colouring across a binary tree."
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/04/11/LCCUP-21-Spring-Team/
  en: https://youngforest.github.io/en/2021/04/11/LCCUP-21-Spring-Team/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (2) |	Q2 (4) |	Q3 (6) |	Q4 (8)| Q5 (9) | Q6(12) |
|--|--|--|--|--|--|--|--|--|--|
| 228 / 781 | 佛系刷题 | 6/41 | 1:00:00 | 0:42:42 | 1:00:00 | null | null | null | null |

[Contest link](https://leetcode-cn.com/contest/season/2021-spring/ranking/team/)

I did not participate in the previous LC-CN spring and fall contests, because our lab used to hold group meetings every Saturday afternoon, which perfectly conflicted with the contest time. Now my advisor has changed to holding small group meetings on weekdays and a large group meeting once a month, so I finally had the chance to participate in the 2021 Spring Contest.

On Monday, during the Qingming Festival, I participated in the solo contest. [Summary blog here](https://youngforest.github.io/en/2021/04/05/LCCUP-21-Spring-Solo/).

On Saturday, I teamed up with Lao Lai and George from the **Buddhist Problem-Solving Group** and set out in a very Buddhist way. The final result was indeed very Buddhist: we ended after two problems. I solved the first problem, and George solved the second problem, though I provided the idea and helped review + debug. I have to say, competing with two teammates was not as effective as me competing alone. No wonder ACM teams need a long time to build chemistry.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LCCUP-21-Spring-Team/en-hero.webp" alt="Upgradeable buckets fill vats in shared rounds while a binary orchard divides a limited connected chain of teal lights" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## LCP 33. Store Water

A warm-up problem. Although it is Easy, as the first problem of a contest, its difficulty was still quite high.

Observation + brute force.

There are two operations in total: upgrade and store water. Obviously, the **store water** operations should happen after **upgrade**.

A naive brute-force method is to enumerate all possible numbers of water-storage operations. The number of upgrades is then determined by that, and we choose the minimum among them. Enumerating the number of water-storage operations also has pruning: try smaller values first, and if it is already larger than the global minimum total count, end directly.

```cpp
class Solution {
public:
    int storeWater(vector<int>& bucket, vector<int>& vat) {
        // time: max(buckets[i]) * buckets.size() = 10^4 * 100
        int ans = numeric_limits<int>::max();
        const int n = bucket.size();
        if (accumulate(vat.begin(), vat.end(), 0) == 0) return 0;
        auto check = [&](const int x) -> int {
            int ans = 0;
            for (int i = 0; i < n; ++i) {
                const int need = vat[i] - bucket[i] * x;
                if (need > 0) ans += (need + x - 1) / x;
            }
            return ans;
        };
        for (int x = 1; x <= 1e4; ++x) { // 蓄水次数
            if (x >= ans) break;
            ans = min(ans, x + check(x));
        }
        return ans;
    }
};
```

Time complexity: O(max(vat[i]) * buckets.length) = O(10^4 * 100),
space complexity: O(1).

## LCP 34. Binary Tree Coloring

DFS. One thing to note is that because the number of nodes in the blue **connected component** is `k`, we need to enumerate how the remaining number of blue nodes at the current node is distributed to the left and right subtrees. Also, observing that `k` is actually small, only 10, further confirmed my guess.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def maxValue(self, root: TreeNode, k: int) -> int:
        @cache
        def dfs(root: TreeNode, remain: int) -> int:
            if not root: return 0
            ans = dfs(root.left, k) + dfs(root.right, k)
            for i in range(0, remain):
                ans = max(ans, dfs(root.left, i) + dfs(root.right, remain - 1 - i) + root.val)
            return ans

        return dfs(root, k)
```

Time complexity: O(number of nodes * k),
space complexity: O(number of nodes * k).

## LCP 35. Electric Car Tour of Cities

## LCP 36. Maximum Number of Card Sets

## LCP 37. Minimum Rectangle Area

## LCP 38. Guard Castle
