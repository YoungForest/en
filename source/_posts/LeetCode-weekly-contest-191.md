---
title: LeetCode Weekly Contest 191
date: 2020-05-31 17:40:09
description: A weekly review of maximum products, widest cake cuts, tree-road redirection and the two-box probability and combinatorics problem missed in contest.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/05/31/LeetCode-weekly-contest-191/
  en: https://youngforest.github.io/en/2020/05/31/LeetCode-weekly-contest-191/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-191/en-hero.webp" alt="The two largest fruits power a product scale, the widest cake gaps meet, tree roads converge on one town and two coloured-ball boxes face an unfinished probability balance" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 765 / 13283 |	YoungForest | 12 | 0:27:19 | 0:02:16 | 0:12:53 | 0:27:19 |  null |

This week's last problem was honestly quite difficult and involved probability, combinatorics, and related knowledge. It happened to hit a blind spot in my knowledge, so I did not solve it. Students who are strong in math should have a much easier time with it.

## 1464. Maximum Product of Two Elements in an Array

A warm-up problem. Since `nums.size()` is relatively small, brute force is enough.

Time complexity: O(N^2),
space complexity: O(1).

```cpp
class Solution {
    const int INF = 0x3f3f3f3f;
public:
    int maxProduct(vector<int>& nums) {
        int ans = -INF;
        for (int i = 0; i < nums.size(); ++i) {
            for (int j = i+1; j < nums.size(); ++j) {
                ans = max(ans, (nums[i] - 1) * (nums[j] - 1));
            }
        }
        return ans;
    }
};
```

## 1465. Maximum Area of a Piece of Cake After Horizontal and Vertical Cuts

Find the maximum horizontal gap and the maximum vertical gap separately, then multiply them. Note that the boundaries should also be treated as cuts.
It is better to use `long long` as the data type, because there can be `int32` overflow issues.

Time complexity: O(horizontalCuts.size() * log + verticalCuts.size() * log),
space complexity: O(1).

```cpp
class Solution {
    using ll = long long;
    const ll MOD = 1e9 + 7;
public:
    int maxArea(int h, int w, vector<int>& horizontalCuts, vector<int>& verticalCuts) {
        horizontalCuts.insert(horizontalCuts.begin(), 0);
        horizontalCuts.push_back(h);
        verticalCuts.insert(verticalCuts.begin(), 0);
        verticalCuts.push_back(w);
        sort (begin(horizontalCuts), end(horizontalCuts));
        sort (begin(verticalCuts), end(verticalCuts));
        int maxH = 0, maxW = 0;
        for (int i = 1; i < horizontalCuts.size(); ++i) {
            maxH = max(maxH, horizontalCuts[i] - horizontalCuts[i-1]);
        }
        for (int j = 1; j < verticalCuts.size(); ++j) {
            maxW = max(maxW, verticalCuts[j] - verticalCuts[j-1]);
        }
        ll mxh = maxH;
        ll mxw = maxW;
        return (mxh * mxw) % MOD;
    }
};
```

## 1466. Reorder Routes to Make All Paths Lead to the City Zero

Because the underlying undirected graph is a tree, each node has exactly one path to 0. So just run DFS from 0 and adjust the edges that are in the reverse direction.

Time complexity: O(N),
space complexity: O(N) -> by recording the parent node, `seen` can be avoided and the space can be reduced to O(1).

```cpp
class Solution {
public:
    int minReorder(int n, vector<vector<int>>& connections) {
        int ans = 0;
        unordered_map<int, vector<int>> in, out;
        vector<bool> seen(n, false);
        for (const auto& v : connections) {
            in[v[1]].push_back(v[0]);
            out[v[0]].push_back(v[1]);
        }
        function<void(int)> dfs = [&](int root) -> void {
            if (seen[root]) return;
            seen[root] = true;
            for (int neighbor : in[root]) {
                dfs(neighbor);
            }
            for (int neighbor : out[root]) {
                if (!seen[neighbor]) {
                    ++ans;
                    dfs(neighbor);
                }
            }
        };
        dfs(0);
        return ans;
    }
};
```

## 1467. Probability of a Two Boxes Having The Same Number of Distinct Balls

A probability problem. Since the constraints are small, backtracking can be used to enumerate the number of balls of each color in the two boxes. At the endpoint, we need to judge the number of legal permutations, meaning the two boxes must contain the same number of balls. Then, if the number of distinct colors is also the same, we record it.
This requires some combinatorics knowledge. Suppose there are `x` balls and the count of each color is `A_i`.
Then the number of permutations is:

$$ x! / \prod_i A_i! $$

Time complexity: O(balls[i]^balls.size() * balls.size() * sum_balls) = O(6 ^ 8 * 8 * 48) = O(644972544).
Space complexity: O(balls.size()).

```python
class Solution:
    def getProbability(self, balls: List[int]) -> float:
        self.all = 0
        self.good = 0
        self.firstHalf = defaultdict(int)
        self.secondHalf = defaultdict(int)
        def validKeys(m):
            ans = 0
            for i in m:
                if m[i] > 0: ans += 1
            return ans
        def permutationUnder(count):
            under = 1
            for k in count.values():
                under *= math.factorial(k)
            return under
        def dfs(i):
            if i == len(balls):
                s1 = sum(self.firstHalf.values())
                s2 = sum(self.secondHalf.values())
                if s1 != s2: return
                add = math.factorial(s1) * math.factorial(s2) / (permutationUnder(self.firstHalf) * permutationUnder(self.secondHalf))
                self.all += add
                if validKeys(self.firstHalf) == validKeys(self.secondHalf): self.good += add
            else:
                for x in range(0, balls[i] + 1):
                    self.firstHalf[i] = x
                    self.secondHalf[i] = balls[i] - x
                    dfs(i+1)
        dfs(0)
        return self.good / self.all
```

