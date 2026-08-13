---
title: LeetCode weekly contest 119
date: 2019-01-13 10:56:25
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/01/13/LeetCode-weekly-contest-119/
  en: https://youngforest.github.io/en/2019/01/13/LeetCode-weekly-contest-119/
---
This contest went rather badly. My rank was roughly 1486 / 3845. The problems were:
- The second problem was relatively simple. Since it was an Easy problem, I directly used brute force and got one TLE. Earlier, because of carelessness, I also wrote the wrong variable in the `for` loop condition once. That caused two penalties.
- The third problem was not very hard either, but in the end I did not think of the O(n) solution. I only thought of an O(n ^ 2) approach. I thought of prefix sums and noticed the keyword divisible, but I did not connect it to the key point that equal prefix sums work.
- For the fourth problem, I thought of DP. I got stuck on the step of "finding the number just slightly larger in the later array", that is, I did not think of using **TreeMap**. Fundamentally, this is because I am not familiar enough with basic data structures.

## 973. K Closest Points to Origin

Directly use `Map` in C++, which sorts by key.

```cpp
class Solution {
public:
    vector<vector<int>> kClosest(vector<vector<int>>& points, int K) {
        map<int, vector<vector<int>>> distance;
        for (const auto point: points) {
            distance[point[0]*point[0] + point[1]*point[1]].push_back(point);
        }
        vector<vector<int>> results;
        int i = 0;
        for (const auto d: distance) {
            for (const auto point: d.second) {
                results.push_back(point);
                i++;
                if (i >= K) {
                    return results;
                }
            }
        }
        
        return results;
    }
};
```


## 976. Largest Perimeter Triangle

Just find the largest three numbers that can form a triangle.

```cpp
class Solution {
public:
    int largestPerimeter(vector<int>& A) {
        int result = 0;
        sort(A.begin(), A.end());
        for (int i = A.size() - 3; i >= 0; i--) {
            if (A[i] + A[i+1] > A[i+2]) {
                result = A[i] + A[i+1] + A[i+2];
                break;
            }
        }
        
        return result;
    }
};
```

## 974. Subarray Sums Divisible by K

```cpp
class Solution {
public:
    int subarraysDivByK(vector<int>& A, int K) {
        unordered_map<int, int> prefix_sum;
        int sum = 0;
        int result = 0;
        prefix_sum[0] = 1;
        for (int i = 0; i < A.size(); i++) {
            sum = (sum + A[i] % K + K) % K;
            result += prefix_sum[sum];
            prefix_sum[sum]++;
        }
        
        return result;
    }
};
```

## 975. Odd Even Jump

In C++, the default implementation of `map` is a Tree Map.

```cpp
class Solution {
public:
    int oddEvenJumps(vector<int>& A) {
        vector<bool> odd_reach(A.size(), false);
        vector<bool> even_reach(A.size(), false);
        int result = 0;
        map<int, int> treemap;
        
        odd_reach[A.size() - 1] = true;
        even_reach[A.size() - 1] = true;
        result++;
        treemap[A[A.size() - 1]] = A.size() - 1;
        
        for (int i = A.size() - 2; i >= 0; --i) {
            auto larger = treemap.lower_bound(A[i]);
            auto smaller = treemap.upper_bound(A[i]);
            
            if (larger != treemap.end()) {
                odd_reach[i] = even_reach[larger->second];
            }
            if (smaller != treemap.begin()) {
                even_reach[i] = odd_reach[(--smaller)->second];
            }
            if (odd_reach[i]) result++;
            treemap[A[i]] = i;
        }
        
        return result;
    }
};
```
