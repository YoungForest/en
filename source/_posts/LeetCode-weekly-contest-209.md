---
title: LeetCode Weekly Contest 209
date: 2020-10-07 11:35:47
description: Contest solutions cover a special array, parity tree, circular viewing window and recursive bit operations; the last was learned after the contest.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/10/07/LeetCode-weekly-contest-209/
  en: https://youngforest.github.io/en/2020/10/07/LeetCode-weekly-contest-209/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-209/en-hero.webp" alt="One threshold step matches the stones above it, alternating tree levels order dark and pale fruit, a circular viewing sector covers the most points and a folded bit machine opens after the contest" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 522 / 12138 | YoungForest | 12 | 0:49:07 | 0:05:12 | 0:11:28 | 0:39:07 2 | null |

## 1608. Special Array With X Elements Greater Than or Equal X

A warm-up problem. Enumerate possible answers from small to large and check each one.

```cpp
class Solution {
public:
    int specialArray(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        const int n = nums.size();
        auto check = [&](const int i) -> bool {
            auto it = lower_bound(nums.begin(), nums.end(), i);
            const int d = distance(nums.begin(), it);
            const int le = n - d;
            return le == i;
        };
        for (int i = 1; i <= 100; ++i) {
            if (check(i)) {
                return i;
            }
        }
        return -1;
    }
};
```

Time complexity: O(N * log N),


## 1609. Even Odd Tree

Level-order traversal of a binary tree. Use Breadth-First Search (BFS), and record the level and the previous element.

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    bool isEvenOddTree(TreeNode* root) {
        // bfs
        queue<TreeNode*> q;
        int level = 0;
        if (root) q.push(root);
        while (!q.empty()) {
            const int s = q.size();
            int last;
            if (level % 2 == 0) last = -1;
            else last = 1'000'006;
            for (int i = 0; i < s; ++i) {
                auto current = q.front();
                q.pop();
                if (level % 2 == 0) {
                    if (current->val % 2 == 0) return false;
                    if (current->val <= last) return false;
                } else {
                    if (current->val % 2 == 1) return false;
                    if (current->val >= last) return false;
                }
                last = current->val;
                if (current->left) q.push(current->left);
                if (current->right) q.push(current->right);
            }
            ++level;
        }
        return true;
    }
};
```

Time complexity: O(N),
space complexity: O(N).

## 1610. Maximum Number of Visible Points

Sliding window. There are two tricks here:
1. Use complex numbers and their methods to handle geometry. This makes it fast to calculate the angle of a vector.
2. Add 360 degrees to every point and traverse twice to handle the wraparound across 0.

Another point to watch out for is that points coinciding with the observer's location need special handling.


```cpp
template <typename T>
ostream& operator <<(ostream& out, const vector<T>& a) {
  out << "["; bool first = true;
  for (auto& v : a) { out << (first ? "" : ", "); out << v; first = 0;} out << "]";
  return out;
}
class Solution {
    using Point = complex<double>;
public:
    int visiblePoints(vector<vector<int>>& points, int a, vector<int>& location) {
        double angle = a;
        const int n = points.size();
        Point me {static_cast<double>(location[0]), static_cast<double>(location[1])};
        vector<double> ps;
        ps.reserve(2 * points.size());
        int same = 0;
        for (const auto & v : points) {
            if (v == location) {
                ++same;
                continue;
            }
            Point x {static_cast<double>(v[0]), static_cast<double>(v[1])};
            ps.push_back(arg(x - me)*(180/M_PI));
        }
        const int s = ps.size();
        for (int i = 0; i < s; ++i) {
            ps.push_back(ps[i] + 360);
        }
        sort(ps.begin(), ps.end());
        // cout << ps << endl;
        const double error = 1e-8;
        int l = 0, r = 0;
        int ans = 0;
        for (; r < ps.size(); ++r) {
            if (ps[r] - ps[l] > angle + error) {
                ++l;
            }
            ans = max(ans, r - l + 1);
            // cout << r << " " << l << endl;
        }
        return min(ans, n) + same;
    }
};
```

Time complexity: O(N log N),
space complexity: O(N).


## 1611. Minimum One Bit Operations to Make Integers Zero

The code was written by referring to [the group owner's video](https://www.youtube.com/watch?v=8MdutrMAwY4). I did not solve it during the contest. After the contest, I read lee215's discuss post too, but it was relatively simple and made some jumps. The group owner's video was still better: it explained things very slowly, almost as if teaching a complete beginner. Although the video is quite long, it is very effective for a difficult problem like this.

```cpp
class Solution {
    int largestBit (const int n) {
        int b = 1;
        while (b <= n) {
            b = (b << 1);
        }
        b = (b >> 1);
        return b;
    }
    int helper(const int n, const int b) {
        if (n == 0) return 1;
        // xxxx -> 1000
        if ((n & b) != 0) {
            // 1xxx -> 1000
            return minimumOneBitOperations(n ^ b);
        } else {
            // 0xxx -> 0100 -> 1100 -> 1000
            return helper(n, b >> 1) + 1 + minimumOneBitOperations(b >> 1);
        }
    }
public:
    int minimumOneBitOperations(int n) {
        if (n == 0) return 0;
        else if (n == 1) return 1;
        if (__builtin_popcount(n) == 1) {
            return (n << 1) - 1;
        } else {
            const int b = largestBit(n);
            return helper(b ^ n, b >> 1) + 1 + minimumOneBitOperations(b >> 1);
        }
        
    }
};
```

Time complexity: O(log N),
space complexity: O(log N).
