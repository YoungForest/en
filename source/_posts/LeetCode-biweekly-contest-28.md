---
title: LeetCode Biweekly Contest 28
date: 2020-06-15 12:53:36
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 448 / 8571 | YoungForest | 14 | 1:22:34 | 0:07:28 | 0:11:43 | null | 1:17:34  1 |

I lost a lot of time debugging the last problem. In the end, I found that the cache in the `range` function was written incorrectly because I modified the function parameters. I must remember in the future to mark memoized parameters as `const`.
For the third problem, I could not think of a more efficient DP solution and kept getting TLE.

## 1475. Final Prices With a Special Discount in a Shop

Find the next eligible discount value. Use a monotonic increasing stack to solve it.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
public:
    vector<int> finalPrices(vector<int>& prices) {
        stack<int> increasing;
        const int n = prices.size();
        vector<int> ans(n);
        for (int i = n - 1; i >= 0; --i) {
            while (!increasing.empty() && increasing.top() > prices[i]) {
                increasing.pop();
            }
            if (increasing.empty()) {
                ans[i] = prices[i];
            } else {
                ans[i] = prices[i] - increasing.top();
            }
            increasing.push(prices[i]);
        }
        return ans;
    }
};
```

## 1476. Subrectangle Queries

Brute force.

Time complexity: 
- updateSubrectangle: O((row2 - row1) * (col2 - col1)),
- getValue: O(1).
Space complexity: O(rows * cols).

```cpp
class SubrectangleQueries {
    vector<vector<int>> rect;
    int rows, cols;
public:
    SubrectangleQueries(vector<vector<int>>& rectangle) : rect(move(rectangle)){
        rows = rect.size();
        cols = rect[0].size();
    }
    
    void updateSubrectangle(int row1, int col1, int row2, int col2, int newValue) {
        for (int i = row1; i <= row2; ++i) {
            for (int j = col1; j <= col2; ++j) {
                rect[i][j] = newValue;
            }
        }
    }
    
    int getValue(int row, int col) {
        return rect[row][col];
    }
};

/**
 * Your SubrectangleQueries object will be instantiated and called as such:
 * SubrectangleQueries* obj = new SubrectangleQueries(rectangle);
 * obj->updateSubrectangle(row1,col1,row2,col2,newValue);
 * int param_2 = obj->getValue(row,col);
 */
```

There is also a [2D segment tree solution](https://leetcode.com/problems/subrectangle-queries/discuss/686094/Python-O(logn)-for-both-update-and-query-2D-Dynamic-Segment-Tree-with-Lazy-Tag) where both functions are `O(log N)`, with preprocessing of `O(N log N)`.

## 1477. Find Two Non-overlapping Sub-arrays Each With Target Sum

Use a prefix array to quickly compute the shortest subarray ending at `i` whose sum is `target`. Update the answer and update the shortest subarray before `i`.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
    const int INF = 0x3f3f3f3f;
public:
    int minSumOfLengths(vector<int>& arr, int target) {
        int ans = INF;
        const int n = arr.size();
        vector<int> best_till(n, INF);
        unordered_map<int, int> presum;
        presum[0] = -1;
        int sum = 0;
        for (int i = 0; i < arr.size(); ++i) {
            sum += arr[i];
            auto it = presum.find(sum - target);
            if (it != presum.end()) {
                int left = it->second;
                int length = i - left;
                if (left >= 0)
                    ans = min(ans, best_till[left] + length);
                if (i > 0)
                    best_till[i] = min(best_till[i-1], length);
                else
                    best_till[i] = length;
            } else {
                if (i > 0)
                    best_till[i] = best_till[i-1];
            }
            presum[sum] = i;
        }
        return ans >= INF ? -1 : ans;
    }
};
```

## 1478. Allocate Mailboxes

Interval DP. Each time, assign one mailbox to the first few houses. The single-mailbox subproblem can be solved greedily.
I wrote the top-down version, which is a bit more convenient to implement than bottom-up.

Time complexity: O(N log N + N * K * N + N ^ 2),
space complexity: O(N * K + N * N).

```cpp
class Solution {
    const int INF = 0x3f3f3f3f;
public:
    int minDistance(vector<int>& houses, int kk) {
        const int n = houses.size();
        sort(houses.begin(), houses.end());
        map<pair<int,int>, int> memo_range;
        auto range = [&](int left, int right) -> int {
            auto it = memo_range.find({left, right});
            if (it != memo_range.end()) return it->second;
            else {            
                int ans = 0;
                int l = left, r = right;
                while (l < r) {
                    ans += houses[r] - houses[l];
                    ++l;
                    --r;
                }
                return memo_range[{left, right}] = ans;
            }
        };
        map<pair<int,int>, int> memo_dp;
        function<int(int,int)> dp = [&](int i, int k) -> int {
            auto it = memo_dp.find({i, k});
            if (it != memo_dp.end()) return it->second;
            else {            
                if (k == 1) {
                    int ans = range(i, n - 1);
                    // cout << "k == 1 " <<  i << " " << k << " " << ans << endl;
                    return memo_dp[{i, k}] = ans;
                } else if (n - i <= k) {
                    return 0;
                } else if (i >= n) {
                    return 0;
                } else {
                    int ans = INF;
                    // n - ni >= k - 1
                    for (int ni = i + 1; ni <= n; ++ni) {
                        // if (i == 2) {
                        //     cout << "2 :: " << ni << " ";
                        //     cout << range(i, ni - 1) << endl;
                        // }
                        ans = min(ans, range(i, ni - 1) + dp(ni, k - 1));
                    }
                    // cout << i << " " << k << " " << ans << endl;
                    return memo_dp[{i, k}] = ans;
                }
            }
        };
        return dp(0, kk);
    }
};
```
