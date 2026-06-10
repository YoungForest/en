---
title: LeetCode biweekly contest 14
date: 2019-12-14 16:12:29
tags:
- Competitive Programming
categories:
- LeetCode
---

I did not go out this week, and happened to run into the biweekly contest. It had been a long time since I joined one, so it really felt rare.
All the problems in this biweekly contest were standard and not hard. I finished everything with one AC on each problem and still had 50 minutes left. A typical speed contest.

| Rank |	Name |	Score |	Finish Time | 	Q1 (2) |	Q2 (4) |	Q3 (5) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 119 / 2503 |	YoungForest | 18 | 0:39:28 | 0:02:18 | 0:08:49 |  0:21:07  | 0:39:28 |

Before the contest ended, my rank was 92, with 1900 participants. After the contest, I found that my rank had dropped. I guessed, and then verified, that the post-contest ranking included users from the China region. The increase in the number of participants and the change in first place matched my guess exactly. I checked leetcode-cn, where the ranking is split into China region and global region. Looks like I need to work even harder in future contests; many people I do not know are competing in the same contests from the China region.

## 1287. Element Appearing More Than 25% In Sorted Array

Use one pass to count the occurrences of all numbers.
Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
public:
    int findSpecialInteger(vector<int>& arr) {
        unordered_map<int, int> count;
        const int target = arr.size() / 4;
        for (int i : arr) {
            ++count[i];
            if (count[i] > target)
                return i;
        }
        return -1;
    }
};
```

The drawback of this solution is that it does not use the constraint given in the problem: the array is already sorted. But this is a warm-up problem, with data size 10^4, and this solution is convenient and quick to implement.

A better solution uses binary search, so each step can advance faster.
Time complexity: O(N log N),
space complexity: O(1).

```cpp
class Solution {
public:
    int findSpecialInteger(vector<int>& arr) {
        auto it = arr.begin();
        while (it != arr.end()) {
            auto it2 = upper_bound(it, arr.end(), *it);
            auto diff = it2 - it;
            if (diff > arr.size() / 4)
                return *it;
            it = it2;
        }
        return -1;
    }
};
```

## 1288. Remove Covered Intervals

The data size is 1000, so an O(N ^ 2) solution can pass.
The most brute-force method is to compare intervals pairwise and remove the ones that should be removed.
My code also uses a trick: first sort by interval size, then compare from large to small. Since small intervals cannot contain large ones, this reduces the number of comparisons. Although the time complexity is unchanged, it is slightly faster in the constant factor.

Time complexity: O(N ^ 2),
space complexity: O(N).

```cpp
class Solution {
public:
    int removeCoveredIntervals(vector<vector<int>>& intervals) {
        set<pair<int, int>> remains;
        sort(intervals.begin(), intervals.end(), [](auto& a, auto& b) -> bool {
            int length_a = a[1] - a[0];
            int length_b = b[1] - b[0];
            return length_b < length_a;
        });
        for (auto& v : intervals) {
            for (auto p : remains) {
                if (p.first <= v[0] && v[1] <= p.second) {
                    goto remove;
                }
            }
            remains.insert({v[0], v[1]});
            remove:;
        }
        return remains.size();
    }
};
```

A better solution: sort first. The sorting rule is that intervals with a smaller left endpoint come first; if the left endpoints are equal, the interval with the larger right endpoint comes first.
Then one pass is enough. Compare the current right endpoint with the maximum right endpoint seen so far.

Time complexity: O(N log N),
space complexity: O(1).

```cpp
class Solution {
public:
    int removeCoveredIntervals(vector<vector<int>>& intervals) {
        sort(intervals.begin(), intervals.end(), [](auto& a, auto& b)  -> bool {
            if (a[0] != b[0]) {
                return a[0] < b[0];
            } else {
                return a[0] > b[0];
            }
        });
        int right = 0;
        int ans = 0;
        for (auto& v : intervals) {
            if (v[1] > right) {
                ++ans;
            }
            right = max(right, v[1]);
        }
        return  ans;
    }
};
```

## 1286. Iterator for Combination

The most brute-force method is to use backtracking to generate all combinations, store them, and wrap them in an iterator. The downsides are: it consumes a lot of space; and when `next` is called only a small number of times, it is less economical than lazy evaluation. Unfortunately I did not know how to implement Python's `yield` in C++, otherwise I could have used lazy evaluation.
Of course, after the contest I still spent some time studying how C++ `yield` could be implemented.
Reference: [C++ yield implementation](how C++ implement yield)

Time complexity: O(C^n_characters.size()),
space complexity: O(combinationLength * C^n_characters.size()).

```cpp
class CombinationIterator {
    void backtracking(vector<string>& results, string& current, int remain, const string& characters, int index) {
        if (remain == 0) {
            results.push_back(current);
            return;
        }
        if (index == characters.size()) {
            return;
        }
        if (remain > characters.size() - index) {
            return;
        }
        // add
        current.push_back(characters[index]);
        backtracking(results, current, remain - 1, characters, index + 1);
        current.pop_back();
        // not add
        backtracking(results, current, remain, characters, index + 1);
    }
    vector<string> results;
    int index = 0;
public:
    CombinationIterator(string characters, int combinationLength) {
        string current;
        backtracking(results, current, combinationLength, characters, 0);
    }
    
    string next() {
        return results[index++];
    }
    
    bool hasNext() {
        return index < results.size();
    }
};

/**
 * Your CombinationIterator object will be instantiated and called as such:
 * CombinationIterator* obj = new CombinationIterator(characters, combinationLength);
 * string param_1 = obj->next();
 * bool param_2 = obj->hasNext();
 */
```

## 1289. Minimum Falling Path Sum II

Like [Minimum Falling Path Sum I](https://leetcode.com/problems/minimum-falling-path-sum/), this is a DP problem.
The state transition equation is:
`dp[row][column] = arr[row][column] + min(dp[row - 1][last_column] for last_column in [0, column_size) and last_column != column)`.
Also, implementing the search for the minimum value in the previous row does not require an extra loop. We only need to store the minimum and second minimum values from the previous row.

Time complexity: O(row_size * column_size),
space complexity: O(row_size * column_size). Another obvious optimization is that `dp` only needs to store the previous row, reducing it to O(column_size).

```cpp
class Solution {
    const int MAX_N = 0x3f3f3f3f;
public:
    int minFallingPathSum(vector<vector<int>>& arr) {
        const int row_size = arr.size();
        const int column_size = arr[0].size();
        vector<vector<int>>  dp(row_size, vector<int> (column_size, 0));
        int row = 0;
        int min_first = MAX_N, min_second = MAX_N;
        for (int column = 0; column < column_size; ++column) {
            dp[row][column] = arr[row][column];
            if (dp[row][column] < min_first) {
                min_second  = min_first;
                min_first = dp[row][column];
            } else if (dp[row][column] < min_second) {
                min_second = dp[row][column];
            }
            // cout << dp[row][column] << " ";
        }
        // cout << " | " << min_first << " " << min_second;
        // cout << endl;
        int last_row_min_first = min_first;
        int last_row_min_second = min_second;
        for (row = 1; row < row_size; ++row) {
            min_first = min_second = MAX_N;
            for (int column = 0; column < column_size; ++column) {
                if (dp[row - 1][column] == last_row_min_first) {
                    dp[row][column] = last_row_min_second + arr[row][column];
                } else {
                    dp[row][column] = last_row_min_first + arr[row][column];
                }           
                if (dp[row][column] < min_first) {
                    min_second  = min_first;
                    min_first = dp[row][column];
                } else if (dp[row][column] < min_second) {
                    min_second = dp[row][column];
                }
                // cout << dp[row][column] << " ";
            }
            // cout << " | " << min_first << " " << min_second;
            // cout << endl;
            last_row_min_first = min_first;
            last_row_min_second = min_second;
        }
        return min_first;
    }
};
```
