---
title: LeetCode weekly contest 188
date: 2020-05-10 18:25:30
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 445 / 12715 |	YoungForest | 19 | 	1:14:29 |  0:07:04 | 0:17:33 | 0:56:49 | 1:14:29 |

My rating has stayed above 2200 for two weeks. Although this week's rating has not been updated yet, based on my rank it should continue to rise. I will try to reach the 2300+ line as soon as possible. Recently I have hit a bottleneck in problem solving. I still cannot solve many hard problems, and I have not summarized my own templates and categories for solving problems. I found that many strong people are strong because after seeing the problem statement, they can quickly identify which concrete category the problem belongs to, quickly connect it with problems they have solved before, and only then AC both fast and bug free.

## 1441. Build an Array With Stack Operations

One pass. Use two indices, one pointing to the position in `target`, and one pointing to the position in `n`.

Time complexity: O(max(N, target.size())),
Space complexity: O(N).

```cpp
class Solution {
public:
    vector<string> buildArray(vector<int>& target, int n) {
        int i = 1;
        int index = 0;
        vector<string> ans;
        for (; i <= n && index < target.size(); ++i) {
            if (target[index] == i) {
                ans.push_back("Push");
                ++index;
            } else {
                ans.push_back("Push");
                ans.push_back("Pop");
            }
        }
        return ans;
    }
};
```

## 1442. Count Triplets That Can Form Two Arrays of Equal XOR

This problem needs to use one property of XOR. `a ^ a = 0`, `a ^ b = b ^ a`, `(a ^ b) ^ c = a ^ (b ^ c)`. That is self-inverse, commutative, and associative.

So we can use an operation similar to prefix sum to calculate the XOR value of an entire interval in O(1).

Then enumerate all triplets and find the ones that satisfy the condition.

Time complexity: O(N ^ 3),
Space complexity: O(N).

```cpp
class Solution {
public:
    int countTriplets(vector<int>& arr) {
        // Time: O(n ^ 3)
        vector<int> prexor(arr.size() + 1);
        prexor[0] = 0;
        // prexor[i]: arr[0] ^ ... ^ arr[i-1]
        for (int i = 0; i < arr.size(); ++i) {
            prexor[i+1] = prexor[i] ^ arr[i]; 
        }
        // [i, j)
        auto xorquick = [&](int i, int j) -> int {
            return prexor[j] ^ prexor[i];
        };
        int ans = 0;
        for (int i = 0; i < arr.size(); ++i) {
            for (int j = i + 1; j < arr.size(); ++j) {
                for (int k = j; k < arr.size(); ++k) {
                    if (xorquick(i, j) == xorquick(j, k + 1)) {
                        ++ans;
                        // cout << "(" << i <a< "," << j << "," << k << ")" << endl;
                    }
                }
            }
        }
        return ans;
    }
};
```

In Discuss, Han Shen gave an
[O(N^2) algorithm and an O(N) algorithm](https://leetcode.com/problems/count-triplets-that-can-form-two-arrays-of-equal-xor/discuss/623747/JavaC%2B%2BPython-One-Pass-O(N4)-to-O(N)).

## 1443. Minimum Time to Collect All Apples in a Tree

Tree DP. First, recursively search whether all subtrees have apples. Then recursively collect apples in the subtrees.

Time complexity: O(N),
Space complexity: O(N).

At first I misread the problem and ignored that we also had to return to the root node, so I wrote a more complicated tree DP. I added a condition for whether it was necessary to come back. This wasted half an hour. Otherwise my rank in this weekly contest could have been a bit higher.

```cpp
class Solution {
    const int INF = 0x3f3f3f3f;
public:
    int minTime(int n, vector<vector<int>>& edges, vector<bool>& hasApple) {
        vector<vector<int>> children(n);
        for (const auto& v : edges) {
            children[v[0]].push_back(v[1]);
            children[v[1]].push_back(v[0]);
        }
        children[0].push_back(-1);
        vector<bool> hasAppleInChildTree(n);
        function<int(int, int)> recurse = [&](int root, int parent) -> int {
            bool ans = hasApple[root];
            for (int child : children[root]) {
                if (child != parent) {
                    if (recurse(child, root)) {
                        ans = true;
                    }
                }
            }
            return hasAppleInChildTree[root] = ans;
        };
        recurse(0, -1);
        function<int(int,int)> dp = [&](int root, int parent) -> int {
           // return step need to collect all apple int subtree root
            if (children[root].size() == 1) return 0;
            int ans = 0;
            for (int child : children[root]) {
                if (child != parent && hasAppleInChildTree[child] == true) {
                    ans += dp(child, root) + 2;
                }
            }
            return ans;
        };
        return dp(0, -1);
    }
};
```

## 1444. Number of Ways of Cutting a Pizza

A typical DP problem.

Each DP step makes one cut.

Time complexity: O(rows * cols * k * (rows + cols)^2),
Space complexity: O(rows * cols * k).

```cpp
class Solution {
    const int MOD = 1e9 + 7;
    using ll = long long;
public:
    int ways(vector<string>& pizza, int k) {
        const int rows = pizza.size();
        const int cols = pizza[0].size();
        auto containApple = [&](int x, int y, int r, int c) -> bool {
            for (int i = x; i < x + r; ++i) {
                for (int j = y; j < y + c; ++j) {
                    if (pizza[i][j] == 'A') {
                        return true;
                    }
                }
            }
            return false;
        };
        map<tuple<int,int,int>, int> memo;
        // time: rows * cols * k * (rows + cols)^2
        function<ll(int, int, int)> dp = [&](int x, int y, int remainCut) -> ll {
            auto it = memo.find({x, y, remainCut});
            if (it != memo.end()) return it->second;
            else {
                ll ans = 0;
                if (remainCut == 0) {
                    ans = containApple(x, y, rows - x, cols - y) ? 1 : 0;
                } else {
                    for (int cutx = x + 1; cutx < rows; ++cutx) {
                        if (containApple(x, y, cutx - x, cols - y)) {
                            ans += dp(cutx, y, remainCut - 1);
                            ans %= MOD;
                        }
                    }
                    for (int cuty = y + 1; cuty < cols; ++cuty) {
                        if (containApple(x, y, rows - x, cuty - y)) {
                            ans += dp(x, cuty, remainCut - 1);
                            ans %= MOD;
                        }
                    }
                }
                return memo[{x, y, remainCut}] = ans;
            }
        };
        return dp(0, 0, k-1);
    }
};
```

## Postscript

During this month and a half in the cruel problem-solving group, I followed the daily check-ins and solved many fairly hard DP problems. My improvement in weekly contests is also obvious. My ranking in the group went from 30 at the beginning to 15. When I joined, there were 170 people in the group, and now there are only 146. So I suspect many strong people left the group, which made my group ranking look better. But the group members and group owner all encouraged me by saying, "It is you who became stronger." I think that is indeed part of the reason.

Looking back at my graduate life, I did not do research well, but I did solve quite a few problems. This week I officially passed 1000+ solved problems.

Thinking back to [when I saw that LeetCode had more than 1000 problems](https://youngforest.github.io/en/2019/03/03/LeetCode-weekly-contest-126/), I was still sighing: how could anyone ever finish them? Now LeetCode has 1400+ problems, and I have also solved 1000+.
