---
title: LeetCode weekly contest 259
date: 2021-09-22 20:31:19
tags:
- Competitive Programming
categories:
- LeetCode
---

[LeetCode weekly contest 259](https://leetcode.com/contest/weekly-contest-259/)

This weekend is Chinese mid-autumn festival, which is a tradition day to got together with family. I am a country boy. My family live in another province, Shanxi. It is about 1,000 km far from Beijing, where I studied and work. Because of that, I cannot visit my parents. Fortunately, my girlfriend grow in Beijing. Her parents invite us to their home for lunch and dinner. They prepared much seafood because their daughter loves it. This is my excuse for not attending this contest.
I solved those problems after contest and share my intuitions and solutions now.

So no score board here.

## 2011. Final Value of Variable After Performing Operations

Sign-in question. A trick, which could reduce 4 operations to 2, is that the middle character (operations[i][1]) decides increment or decrement. With this, we could simplify our code.

```cpp
class Solution {
public:
    int finalValueAfterOperations(vector<string>& operations) {
        int ans = 0;
        for (const auto& s : operations) {
            if (s[1] == '-') {
                --ans;
            } else {
                ++ans;
            }
        }
        return ans;
    }
};
```

Time complexity: O(N), where `N = operations.size()`
Space complexity: O(1).

## 2012. Sum of Beauty in the Array

For score is 2, compare `nums[i]` with the largest number before `i` and the smallest number after `i`. We maintain a variable to record and update `the largest number` and a pre-calculate array to record `the smallest number`.

For score is 1, just compare `nums[i]` with `nums[i-1]` and `nums[i+1]`.

```cpp
class Solution {
public:
    int sumOfBeauties(vector<int>& nums) {
        const int n = nums.size();
        vector<int> smallestNumber(n + 1);
        smallestNumber[n] = numeric_limits<int>::max();
        for (int i = n-1; i >= 0; --i) {
            smallestNumber[i] = min(nums[i], smallestNumber[i+1]);
        }
        int largestNumber = nums[0];
        int ans = 0;
        for (int i = 1; i < n - 1; ++i) {
            int score = 0;
            if (largestNumber < nums[i] && nums[i] < smallestNumber[i+1]) {
                score = 2;
            } else if (nums[i-1] < nums[i] && nums[i] < nums[i+1]) {
                score = 1;
            }
            largestNumber = max(largestNumber, nums[i]);
            ans += score;
        }
        return ans;
    }
};
```

Time complexity: O(n),
Space complexity: O(n).

## 2013. Detect Squares

Attention: **Square** instead of **rectangle**.
I wasted a lot of time because of this misunderstanding. Luckily, it is not on real contest.

Use `unordered_map<int, map<int, int>>` to store all points.
When count squares, we iterate every row and find the width, then with width find the other two points.

```cpp
class DetectSquares {
    static constexpr int MX = 1001;
    unordered_map<int, map<int, int>> points;
public:
    DetectSquares() {
        
    }
    
    void add(vector<int> point) {
        ++points[point[0]][point[1]];
    }
    
    int count(vector<int> point) {
        // iterate in Y axis, iterate in X axis
        // time complexity: n
        const int x = point[0];
        const int y = point[1];
        const auto& row = points[x];
        int ans = 0;
        for (const auto& p : points) {
            if (p.first == x) continue;
            const auto& row2 = p.second;
            auto it = row2.find(y);
            if (it != row2.end()) {
                // find D
                const int width = abs(p.first - x);
                for (int i : {+width, -width}) {
                    auto itB = row.find(y + i);
                    auto itC = row2.find(y + i);
                    if (itB != row.end() && itC != row2.end()) {
                        ans += it->second * itB->second * itC -> second;
                    }
                }
            }
        }
        return ans;
    }
};

/**
 * Your DetectSquares object will be instantiated and called as such:
 * DetectSquares* obj = new DetectSquares();
 * obj->add(point);
 * int param_2 = obj->count(point);
 */
```

Time complexity:
- DetectSquares: O(1),
- add: O(log n),
- count: O(n log n).
Space complexity: O(n).
Where n = points number.

## 2014. Longest Subsequence Repeated k Times