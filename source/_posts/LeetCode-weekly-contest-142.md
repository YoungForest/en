---
title: LeetCode weekly contest 142
date: 2019-06-23 23:09:42
description: Weekly notes on sample statistics, car-pool capacity, mountain-array searches and brace expansion after a comparator bug.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/06/23/LeetCode-weekly-contest-142/
  en: https://youngforest.github.io/en/2019/06/23/LeetCode-weekly-contest-142/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-142/en-hero.webp" alt="Forest repairs a contradictory sorting balance beside a sample gauge, car-pool stops, mountain probes and branching parser" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (5) |	Q2 (5) |	Q3 (8) |	Q4 (8)|
|--|--|--|--|--|--|--|--|
| 851 / 4504 |	YoungForest | 13 | 1:39:38 | null |  1:00:12 2 | 1:19:38 2 | null |

The main mistake in this contest was that I wrote the `cmp` function in `sort` incorrectly for the second problem and did not guarantee strict ordering. It kept causing segmentation faults. That is, if `a < b`, then necessarily `b !< a`.

## 1093. Statistics from a Large Sample
Intution:
You only need to be familiar with C++ and the meaning of these five statistical values.
Time complexity: O(N),
Space complexity: O(1).

```cpp
class Solution {
public:
    vector<double> sampleStats(vector<int>& count) {
        double minimum = 255.0, maximum = 0.0, mean = 0.0, median = 0.0, mode = 0.0;
        auto minimum_it = find_if(count.cbegin(), count.cend(), [](const auto & a) -> bool {
            return a > 0;
        });
        minimum = minimum_it - count.cbegin();
        auto max_it = find_if(count.crbegin(), count.crend(), [](const auto & a) -> bool {
            return a > 0;
        });
        maximum = static_cast<int>(count.size()) - (max_it - count.crbegin()) - 1;
        
        int num = 0;
        for (int i = 0; i < count.size(); ++i) {
            mean += i * count[i];
            num += count[i];
        }
        mean /= num;
        int total = num;
        num = 0;
        for (int i = 0; i < count.size(); ++i) {
            if (count[i] == 0)
                continue;
            num += count[i];
            if (num * 2 > total) {
                if (median > 0) {
                    median = (median + i) / 2;
                } else {
                    median = i;
                }
                break;
            } else if (num * 2 == total) {
                median = i;
            }
        }
        
        auto mode_it = max_element(count.cbegin(), count.cend());
        mode = mode_it - count.cbegin();
        
        return {minimum, maximum, mean, median, mode};
    }
};
```

## 1094. Car Pooling

Intuition:
Simulate the whole process of passengers getting on and off. At each stop, check whether the capacity is exceeded.
Time complexity: O(N log N)
Space complexity: O(N)

```cpp
class Solution {
public:
    bool carPooling(vector<vector<int>> &trips, int capacity) {
       map<int, int> stops;
       for (const auto& trip : trips) {
           stops[trip[1]] += trip[0];
           stops[trip[2]] -= trip[0];
       }
       int passengers = 0;
       for (const auto& stop : stops) {
           passengers += stop.second;
           if (passengers > capacity)
               return false;
       }
       return true;
    }
};
```

## 1095. Find in Mountain Array

This problem counts as a new type of problem: an interactive problem.
Three binary searches solve it.

Time complexity: O(log N).
Space complexity: O(1).

```cpp
/**
 * // This is the MountainArray's API interface.
 * // You should not implement it, or speculate about its implementation
 * class MountainArray {
 *   public:
 *     int get(int index);
 *     int length();
 * };
 */
class Solution {
public:
    int findInMountainArray(int target, MountainArray &mountainArr) {
        int lo = 0, hi = mountainArr.length();
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (mid == mountainArr.length() - 1) {
                hi = mid;
                continue;
            }
            if (mountainArr.get(mid) < mountainArr.get(mid + 1))
                lo = mid + 1;
            else 
                hi = mid;
        }
        
        int mountain = lo;
        // cout << "mountain: " << mountain << endl;
        lo = 0, hi = mountain + 1;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (mountainArr.get(mid) < target)
                lo = mid + 1;
            else 
                hi = mid;
        }
        // cout << "first: " << lo << endl;
        if (mountainArr.get(lo) == target)
            return lo;
        lo = mountain, hi = mountainArr.length();
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (mountainArr.get(mid) > target)
                lo = mid + 1;
            else 
                hi = mid;
        }
        // cout << "second: " << lo << endl;
        
        if (lo < mountainArr.length() && mountainArr.get(lo) == target)
            return lo;
        
        return -1;
    }
};
```

## 1096. Brace Expansion II

Referenced this [discuss](https://leetcode.com/problems/brace-expansion-ii/discuss/317732/ChineseC++-1096.).
The author used parsing to solve this problem.
The two subroutines `parseRule2` and `parseRule3` handle union and Cartesian product respectively.

Time complexity: O(N^N), because of the Cartesian product.

```cpp
class Solution {
    set<string> descarte(const set<string>& s1, const set<string>& s2) {
        set<string> ret;
        for (const auto& a : s1) {
            for (const auto& b : s2) {
                ret.insert(a + b);
            }
        }
        return ret;
    }
    // {a,b},a,{a}
    set<string> parseRule2(const string& s, int& i) {
        set<string> ret = parseRule3(s, i);
        while (i < s.size()) {
            if (s[i] != ',')
                break;
            ++i;    // skip ','
            auto temp = parseRule3(s, i);
            ret.insert(std::make_move_iterator(temp.begin()), std::make_move_iterator(temp.end()));
        }
        return ret;
    }
    // {a,b}b{a}
    set<string> parseRule3(const string& s, int& i) {
        set<string> ret;
        ret.insert("");
        while (i < s.size()) {
            if (s[i] == '}' || s[i] == ',')
                break;
            if (s[i] == '{') {
                ++i;    // skip '{'
                auto temp = parseRule2(s, i);
                ++i;    // skip '{'
                ret = descarte(ret, temp);
            } else {
                set<string> new_ret;
                for (auto& item : ret) {
                    new_ret.insert(item + s[i]);
                }
                ret = std::move(new_ret);
                ++i;
            }
        }
        return ret;
    }
    
    
    
public:
    vector<string> braceExpansionII(string expression) {
        int pos = 0;
        auto ret = parseRule2(expression, pos);
        ret.erase("");
        return vector<string> (std::make_move_iterator(ret.begin()), std::make_move_iterator(ret.end()));
    }
};
```
