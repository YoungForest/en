---
title: LeetCode Weekly Contest 182
date: 2020-03-29 12:42:45
description: Weekly notes on lucky frequencies, rising and falling teams, transit-time design and valid-string counting under practice-group pressure.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/03/29/LeetCode-weekly-contest-182/
  en: https://youngforest.github.io/en/2020/03/29/LeetCode-weekly-contest-182/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-182/en-hero.webp" alt="Forest holds a blank amber envelope near matching pebble pedestals, rising and falling trios, underground circles and colour-bead gates" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (8)|
|--|--|--|--|--|--|--|--|
| 727 / 11694 |	YoungForest | 12 | 	0:22:50 | 0:03:13 | 0:14:04 |  0:22:50 | null |

This Monday I joined a LeetCode daily check-in and weekly contest group. It publishes rankings every week, and the bottom-ranked person sends a red packet; every day there is a designated problem to solve, and if you miss two consecutive days you also send a red packet. It is extremely intense and exciting. [Contest leaderboard](https://wisdompeak.github.io/lc-score-board/).
This was my first contest since joining the group. Because the fourth problem was too hard, only around one hundred people solved it in total. In the group, only five people got AC.

## 1394. Find Lucky Integer in an Array

A warm-up problem. Pay attention to the data range and just count frequency.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
public:
    int findLucky(vector<int>& arr) {
        vector<int> count(501, 0);
        for (int i : arr) {
            ++count[i];
        }
        for (int i = 500; i >= 1; --i) {
            if (count[i] == i)
                return i;
        }
        return -1;
    }
};
```

## 1395. Count Number of Teams

Because the data scale is small, even an `N^3` solution that enumerates all triples can pass.
Here I used DP, with `N^2` complexity. If using an order statistic tree, it can be further reduced to `N log N`.

Time complexity: O(N ^ 2),
space complexity: O(N).

```cpp
class Solution {
public:
    int numTeams(vector<int>& rating) {
        auto f = [&](function<bool(int,int)> op) -> int {
            const int n = rating.size();
            vector<vector<int>> large(3, vector<int>(n, 1));
            int ans;
            for (int echo = 1; echo <= 2; ++echo) {
                ans = 0;
                for (int i = 0; i < n; ++i) {
                    large[echo][i] = 0;
                    for (int j = 0; j < i; ++j) {
                        if (op(rating[i], rating[j])) {
                            large[echo][i] += large[echo-1][j];
                        }
                    }
                    ans += large[echo][i];
                }
            }
            return ans;
        };
        return f(greater<int>()) + f(less<int>());
    }
};
```

## 1396. Design Underground System

Straightforward.
Use a `map` to record the total time and number of people for each pair of stations.

Time complexity:
- checkIn: O(1),
- checkOut: O(1 + log N),
- getAverageTime: O(log N).
Space complexity: O(user.size() + station.size() ^ 2).

```cpp
class UndergroundSystem {
    unordered_map<int, pair<string, int>> user_checkin_time;    // 记录进站信息
    map<pair<string, string>, pair<int, int>> interval_total_time_person_count; // 记录2站之间流量的信息，总时间、人数
public:
    UndergroundSystem() {
        
    }
    
    void checkIn(int id, string stationName, int t) {
        user_checkin_time[id] = {stationName, t};
    }
    
    void checkOut(int id, string stationName, int t) {
        auto in = user_checkin_time[id];
        auto& interval = interval_total_time_person_count[{in.first, stationName}];
        interval.first += t - in.second;
        ++interval.second;
        user_checkin_time.erase(id);
    }
    
    double getAverageTime(string startStation, string endStation) {
        auto it = interval_total_time_person_count.find({startStation, endStation});
        if (it == interval_total_time_person_count.end())
            return -1;
        else
            return static_cast<double>(it->second.first) / it->second.second;
    }
};

/**
 * Your UndergroundSystem object will be instantiated and called as such:
 * UndergroundSystem* obj = new UndergroundSystem();
 * obj->checkIn(id,stationName,t);
 * obj->checkOut(id,stationName,t);
 * double param_3 = obj->getAverageTime(startStation,endStation);
 */
```

## 1397. Find All Good Strings
