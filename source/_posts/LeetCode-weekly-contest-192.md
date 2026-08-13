---
title: LeetCode Weekly Contest 192
date: 2020-06-07 22:36:35
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/06/07/LeetCode-weekly-contest-192/
  en: https://youngforest.github.io/en/2020/06/07/LeetCode-weekly-contest-192/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 374 / 13805 |	YoungForest | 18 | 	0:53:48 | 0:07:19 | 0:07:35 | 0:15:00 | 0:43:48 2 |

This week's problems were not too hard. It was a speed contest for problems 3, 4, 5, and 6, and the last 1,000 people got AK.
I was reasonably fast on the first three problems, but spent a relatively long time on the last one and got TLE twice because of implementation issues. I originally thought I did pretty well, but after seeing the ranking I realized everyone was very strong. I still need to keep working hard and try to get my rating into the global top 500.

## 1470. Shuffle the Array

Use an auxiliary array. Straightforward.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
public:
    vector<int> shuffle(vector<int>& nums, int n) {
        vector<int> ans(2 * n);
        for (int i = 0; i < n; ++i) {
            ans[i*2] = nums[i];
            ans[i*2 + 1] = nums[n + i];
        }
        return ans;
    }
};
```

## 1471. The k Strongest Values in an Array

Use `stronger` as the sorting function and sort the whole `arr`.

Time complexity: O(N log N),
space complexity: O(1).

```cpp
class Solution {
public:
    vector<int> getStrongest(vector<int>& arr, int k) {
        sort(arr.begin(), arr.end());
        const int n = arr.size();
        int m = arr[((n - 1) / 2)];
        sort(arr.begin(), arr.end(), [&](const auto& a, const auto& b) -> bool {
            if (abs(a - m) == abs(b - m)) {
                return a > b;
            } else {
                return abs(a - m) > abs(b - m);
            }
        });
        arr.erase(arr.begin() + k, arr.end());
        return arr;
    }
};
```

## 1472. Design Browser History

Simulate the whole process. Use an array to store `history`, and two indices to represent the current position and the latest position.

Time complexity: O(1),
space complexity: O(N).

```cpp
class BrowserHistory {
    vector<string> history;
    int index;
    int last;
public:
    BrowserHistory(string homepage) {
        history.resize(5000);
        index = 0;
        last = 0;
        history[0] = move(homepage);
    }
    
    void visit(string url) {
        ++index;
        history[index] = url;
        last = index;
    }
    
    string back(int steps) {
        if (index >= steps) {
            index = index - steps;
        } else {
            index = 0;
        }
        return history[index];
    }
    
    string forward(int steps) {
        if (index + steps <= last) {
            index = index + steps;
        } else {
            index = last;
        }
        return history[index];
    }
};

/**
 * Your BrowserHistory object will be instantiated and called as such:
 * BrowserHistory* obj = new BrowserHistory(homepage);
 * obj->visit(url);
 * string param_2 = obj->back(steps);
 * string param_3 = obj->forward(steps);
 */
```

## 1473. Paint House III

DP.
`dp[i][t][lastColor]`: paint the houses in `[i:]`, split them into `t` groups, and the previous color is `lastColor` (to avoid merging groups with the same color). It represents the minimum required cost.

State transition: `dp[i][t][lastColor] = min(paint cost [i:j) use color + dp[j][t-1][color]) for j in range(i,m+1) for color in range(1,n+1)`. 
Some pruning and an efficient implementation of the `paint` function are needed. I got TLE twice because of this.

Time complexity: O(m * target * n * m * n),
space complexity: O(m * target * n).

```python
class Solution:
    def minCost(self, houses: List[int], cost: List[List[int]], m: int, n: int, target: int) -> int:
        @lru_cache(None)
        def paint(i, j, color):
            if i == j:
                return 0
            else:
                if houses[i] == color:
                    return paint(i+1, j, color)
                elif houses[i] == 0:
                    return paint(i+1, j, color) + cost[i][color-1]
                else:
                    return float('inf')
            
        @lru_cache(None)
        def dp(i: int, t: int, lastColor: int) -> int:
            if t == 1:
                ans = float('inf')
                for color in range(1, n + 1):
                    if color == lastColor: continue
                    pre = paint(i, m, color)
                    ans = min(ans, pre)
                return ans
            else:
                ans = float('inf')
                seenColors = 0
                for j in range(i + 1, m):
                    if houses[j-1] != 0:
                        if seenColors != 0 and seenColors != houses[j-1]:
                            return ans
                        seenColors = houses[j-1]
                    if seenColors == 0:
                        for color in range(1, n + 1):
                            if color == lastColor: continue
                            pre = paint(i, j, color)
                            if pre < ans:
                                ans = min(ans, pre + dp(j, t-1, color))
                    else:
                        color = seenColors
                        if color != lastColor: 
                            pre = paint(i, j, color)
                            if pre < ans:
                                ans = min(ans, pre + dp(j, t-1, color))
                return ans
        
        ans = dp(0, target, -1)
        return ans if ans < float('inf') else -1
```
