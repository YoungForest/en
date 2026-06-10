---
title: LeetCode weekly contest 137
date: 2019-05-19 14:35:35
tags:
- Competitive Programming
categories:
- LeetCode
---

This week's problems were harder than usual. You could also say they happened to hit my blind spot: DP problems. To be honest, I have not studied DP problems deeply. This contest had especially many DP problems, especially the fourth problem, which can be solved with the classic **knapsack problem**.

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (6) |	Q4 (8)|
|--|--|--|--|--|--|--|--|
| 576 / 4091 |	YoungForest | 13 | 	0:45:56 | 0:09:24 | 0:14:20 |	0:35:56  2 | null |

## 1046. Last Stone Weight

Intuition:
The solution to this problem is not hard. I first thought of the most brute-force solution, simulating the whole smash process. Because it is a warm-up problem, brute force is enough.
Time complexity: O(n^2 log n)
Space complexity: O(1)
```cpp
class Solution {
public:
    int lastStoneWeight(vector<int>& stones) {
        // 模拟smash
        // sort
        // pick two heaviest
        // smash
        // n^2 log n
        while (stones.size() > 1) {
            sort(stones.begin(), stones.end());
            int n = stones.size();
            stones[n - 2] = stones[n - 1] - stones[n - 2];
            stones.pop_back();
        }
        return stones[0];
    }
};
```

But in fact, this is also simulating the smash process and finding the two largest stones. My method uses `sort`, whose time complexity is `O(n log n)`. Stronger programmers can think of a Priority Queue, where the time complexity for finding stones is `O(log n)`. Also, my solution does not specially handle the `x == y` case. Although it does not cause problems, it indeed does not match the original intent of the simulation.

### Priority queue version
Time complexity: O(N log N)
Space complexity: O(1)
```cpp
class Solution {
public:
    int lastStoneWeight(vector<int>& stones) {
        priority_queue<int> pq(stones.begin(), stones.end());
        while (pq.size() > 1) {
            int y = pq.top();
            pq.pop();
            int x = pq.top();
            pq.pop();
            if (y > x)
                pq.push(y - x);
        }
        return pq.empty() ? 0 : pq.top();
    }
};
```

## 1047. Remove All Adjacent Duplicates In String
Intuition:
Use a stack to store previous characters, read the next character in order, and remove based on the condition.
Time complexity: `O(N)`,
Space complexity: `O(N)`.

```cpp
class Solution {
public:
    string removeDuplicates(string S) {
        stack<char> s;
        for (char c : S) {
            if (s.empty()) {
                s.push(c);
            } else if (s.top() == c) {
                s.pop();
            } else {
                s.push(c);
            }
        }
        string ret;
        while (!s.empty()) {
            ret.push_back(s.top());
            s.pop();
        }
        reverse(ret.begin(), ret.end());
        return ret;
    }
};
```

Using a stack should be the standard solution for this problem. Stronger programmers basically use the same method.

## 1048. Longest String Chain

Intuition:
DP. For each word, iterate over all words that are shorter by 1 and update to the maximum `dp + 1`.
Time complexity: `O(N^2 * word.length)`
Space complexity: `O(N)`.
Here N is `words.length`.

```cpp
class Solution {
    bool isPredecessor(const string& last, const string& current) {
        if (last.size() + 1 != current.size())
            return false;
        int flag = 0;
        for (int i = 0; i < last.size();) {
            if (last[i] == current[i + flag]) {
                ++i;
                continue;
            }
            if (flag == 0)
                ++flag;
            else
                return false;
        }
        return true;
    }
public:
    int longestStrChain(vector<string>& words) {
        vector<vector<pair<string, int>>> dp(16);
        for (const auto& s : words) {
            dp[s.size() - 1].push_back({s, 1});
        }
        // for (const auto& i : dp) {
        //     for (const auto& j : i) {
        //         cout << j.first << " ";
        //     }
        //     cout << endl;
        // }
        int ret = 1;
        for (int i = 1; i < 16; ++i) {
            int last_length = i - 1;
            const auto& last_dp = dp[last_length];
            auto& current_dp = dp[i];
            for (auto& current : current_dp) {
                for (const auto& last : last_dp) {
                    if (isPredecessor(last.first, current.first)) {
                        current.second = max(current.second, last.second + 1);
                        ret = max(ret, current.second);
                    }
                }
            }
        }
        // for (const auto& i : dp) {
        //     for (const auto& j : i) {
        //         cout << "(" << j.first << " " << j.second << "), ";
        //     }
        //     cout << endl;
        // }
        return ret;
    }
};
```

Discuss summary: there are roughly only two methods, DP (bottom-up) and DFS. DFS has higher complexity and needs memoization, which is actually top-down DP.

## 1049. Last Stone Weight II

Intuition:
backtracking. 
Time complexity: O(N!), N = 30. So it will definitely time out.

In addition, I also tried a Greedy method.
But I did not find the correct solution or prove it. I tried twice and both were Wrong Answer.

**Correct idea**:
Consider a remaining stone after one smash, `y - x`. If it is smashed again, the remaining stone is `z - (y - x) = z - y + x`.
For any original stone, if the number of times it is smashed is odd, it has a negative sign in front; otherwise, it has a positive sign.
Therefore, the original problem can be transformed into splitting all stones into two groups and minimizing the absolute difference between the two groups' sums.
This problem is one of the classic knapsack problems, "minimum knapsack partition."

### Solution: DP for classic knapsack problem
```cpp
class Solution {
public:
    int lastStoneWeightII(vector<int>& stones) {
        const int sum_bound = 1500;
        bitset<1500 + 1> dp {1}; // present whether the sum of group is i is possible; initial true when i == 0  
        int sumOfStones = 0;
        for (auto stone : stones) {
            sumOfStones += stone;
            for (int i = sum_bound; i >= stone; --i) {  // reverse traversal is needed, for a stone can be used only once
                dp[i] = dp[i] + dp[i - stone];
            }
        }
        int ret = numeric_limits<int>::max();
        for (int i = 0; i <= sum_bound; ++i) {
            ret = min(ret, abs(sumOfStones - dp[i]*i*2));
        }
        return ret;
    }
};
```

Time complexity: O(sum_bound * stones.size())
Space complexity: O(sum_bound)

In C++, `bitset` is an array containing bits or Boolean values with a fixed size. If you are not familiar with it, you can read [*The C++ Standard Library, 2nd Edition*](https://book.douban.com/subject/26419721/). I have also been reading it recently, with the goal of becoming familiar with the C++ standard library and learning its design and usage.
Besides using `bitset`, you can also directly use `set`.

## Afterword

On Friday I bought a one-year LeetCode Premium subscription for 159 dollars, roughly 1000 RMB.
Time to grind problems!!!
