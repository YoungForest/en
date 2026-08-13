---
title: LeetCode Weekly Contest 213
date: 2020-11-01 11:16:44
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/11/01/LeetCode-weekly-contest-213/
  en: https://youngforest.github.io/en/2020/11/01/LeetCode-weekly-contest-213/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 139 / 10630 | YoungForest | 18 | 0:39:13 | 0:06:26 | 0:14:56 |   0:22:59 |  0:39:13 |

I have performed well in weekly contests for four consecutive weeks. Happy. My group ranking has also stabilized at 15th. In the end, I still cannot enter the top 10, but I am already satisfied.

## 5554. Check Array Formation Through Concatenation

Warm-up problem. Note that both `arr` and `pieces` are distinct.
So we only need to record the array corresponding to the first element of each piece, look it up directly, and match.

```cpp
class Solution {
public:
    bool canFormArray(vector<int>& arr, vector<vector<int>>& pieces) {
        unordered_map<int, int> num2indexInPieces;
        for (int i = 0; i < pieces.size(); ++i) {
            const auto& v = pieces[i];
            num2indexInPieces[v[0]] = i;
        }
        for (int i = 0; i < arr.size(); ) {
            auto it = num2indexInPieces.find(arr[i]);
            if (it == num2indexInPieces.end()) return false;
            const int j = it->second;
            const int oldi = i;
            for (; i < arr.size() && i - oldi < pieces[j].size(); ++i) {
                if (arr[i] != pieces[j][i - oldi]) return false;
            }
        }
        return true;
    }
};
```

Time complexity: O(arr.size() + pieces.size()),
space complexity: O(pieces.size()).

## 5555. Count Sorted Vowel Strings

DP. Define `dp(n, i)` as the number of strings of length n whose first character is the i-th largest letter.
`dp(n, i) = sum(dp(n-1, j) for j in range(i, 0, -1))`.

```python
class Solution:
    def countVowelStrings(self, n: int) -> int:
        @lru_cache(None)
        def dp(n, i):
            if n == 1:
                return i
            else:
                return sum(dp(n-1, j) for j in range(i, 0, -1))
        return dp(n, 5)
```

Time complexity: O(n * 5 * 5),
space complexity: O(n * 5).

Of course, there is also [zerotrac's O(1) solution](https://leetcode-cn.com/problems/count-sorted-vowel-strings/solution/tong-ji-zi-dian-xu-yuan-yin-zi-fu-chuan-de-shu-mu-/) in the comments. Too strong. Anyway, I did not understand it.


## 5556. Furthest Building You Can Reach

Greedy. Use ladders as much as possible on the largest height differences.
Here we need a `priority_queue` to maintain the largest previous height differences.

```cpp
class Solution {
public:
    int furthestBuilding(vector<int>& heights, int bricks, int ladders) {
        std::priority_queue<int, std::vector<int>, std::greater<int> > q;
        int lasth = heights[0];
        int i = 1;
        for (; i < heights.size(); ++i) {
            if (heights[i] > lasth) {
                const int diff = heights[i] - lasth;
                q.push(diff);
                if (q.size() > ladders) {
                    int t = q.top();
                    q.pop();
                    bricks -= t;
                    if (bricks < 0) return i - 1;
                }
            }
            lasth = heights[i];
        }
        return heights.size() - 1;
    }
};
```

## 5600. Kth Smallest Instructions

Two weeks ago, a Hulu interview asked me about an algorithm for finding the k-th largest number in a binary search tree. That was also one of the implementations I first saw in *Algorithms, 4th Edition*. In regular problem solving, I also often need this kind of rank-aware data structure.

This problem is very similar to the idea of finding rank: maintain the size of a subtree, then choose the walking decision based on the relative size of the "left subtree, meaning going right" and the rank. The difference is that in this problem, the subtree size is the number of possible paths, which is a combination count.

This problem uses zerotrac's method of computing combinations with Pascal's triangle. Compared with a factorial algorithm, it supports modulo and also avoids overflow.

```cpp
class Solution {
    using ll = unsigned long long;
    const ll MOD = 1e18 + 7;
    vector<vector<ll>> c;
    ll C(ll m, ll n) {
        return c[m][n];
    }
public:
    string kthSmallestPath(vector<int>& destination, int k) {
        const int rows = destination[0];
        const int cols = destination[1];
        const  int n = rows + cols + 1;
        c.resize(n + 1, vector<ll>(n + 1));
        c[0][0] = 1;
        for (int i = 1; i <= n; ++i) {
            c[i][0] = 1;
            for (int j = 1; j <= i; ++j) {
                c[i][j] = c[i - 1][j - 1] + c[i - 1][j];
                if (c[i][j] >= MOD) {
                    c[i][j] -= MOD;
                }
            }
        }
        string ans;
        using pii = pair<int, int>;
        pii start = {0, 0};
        int r = rows, c = cols;
        while (ans.size() < rows + cols) {
            if (r == 0) {
                ans.push_back('H');
                --c;
            } else if (c == 0) {
                ans.push_back('V');
                --r;
            } else {
                const ll right = C(r + c - 1, c - 1);
                if (right >= k) {
                    ans.push_back('H');
                    --c;
                } else {
                    ans.push_back('V');
                    --r;
                    k -= right;
                }
            }
        }
        return ans;
    }
};
```

Time complexity: O((rows + cols) * cols),
space complexity: O((rows + cols) * cols).
