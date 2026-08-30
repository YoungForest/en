---
title: LeetCode Biweekly Contest 36
date: 2020-10-06 09:29:23
description: A biweekly review covers parking capacity, access alerts, row-column matrices and server rotation; only the first two were solved during the contest.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/10/06/LeetCode-biweekly-contest-36/
  en: https://youngforest.github.io/en/2020/10/06/LeetCode-biweekly-contest-36/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-biweekly-contest-36/en-hero.webp" alt="Three vehicle sizes consume matching bays, access tokens share one hourglass window, row and column vessels fill a grid and a repaired server carousel redistributes requests" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (6) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 1667 / 8332 | YoungForest | 7 | 0:15:37 | 0:01:46 | 0:15:37 | null | null |

I crashed again in this biweekly contest. Life really is full of ups and downs and then more downs... Problems 3 and 4 were actually still possible to solve, but my state during the contest was bad and I made the wrong decision. After looking at problem 3 for two minutes without an idea, I switched to problem 4. Then I overcomplicated problem 4, spent quite a bit of time implementing it, and still got stuck with TLE in the end.

## 1603. Design Parking System

A warm-up problem. Just maintain the number of remaining parking spots for each type.

```cpp
class ParkingSystem {
    array<int, 4> cnt;
public:
    ParkingSystem(int big, int medium, int small) {
        cnt[1] = big;
        cnt[2] = medium;
        cnt[3] = small;
    }
    
    bool addCar(int carType) {
        if (cnt[carType] > 0) {
            --cnt[carType];
            return true;
        } else return false;
    }
};

/**
 * Your ParkingSystem object will be instantiated and called as such:
 * ParkingSystem* obj = new ParkingSystem(big, medium, small);
 * bool param_1 = obj->addCar(carType);
 */
```

Time complexity: O(addCar calls),
space complexity: O(1).

## 1604. Alert Using Same Key-Card Three or More Times in a One Hour Period

Sort by time first, then traverse in order. Record each person's entry times and check whether the last three entries fall within the same hour.

```cpp
class Solution {
public:
    vector<string> alertNames(vector<string>& keyName, vector<string>& keyTime) {
        using pss  = pair<string, string>;
        set<string> ans;
        const int n = keyName.size();
        vector<pss> records;
        records.reserve(n);
         for (int i = 0; i < n; ++i) {
             records.emplace_back(keyTime[i], keyName[i]);
         }
        sort(records.begin(), records.end());
        int hour = 0;
        unordered_map<string, vector<string>> enter;
        auto convert2int = [&](const string& x) -> int {
            int h = stoi(x.substr(0, 2));
            int m = stoi(x.substr(3));
            return h * 60 + m;
        };
        auto check = [&](const string& x) -> bool {
            if (enter[x].size() < 3) return false;
            else {
                int a = convert2int(enter[x][enter[x].size() - 1]);
                int b = convert2int(enter[x][enter[x].size() - 2]);
                int c = convert2int(enter[x][enter[x].size() - 3]);
                return a - c <= 60;
            }
        };
        for (int i = 0; i < n; ++i) {
            enter[records[i].second].push_back(records[i].first);
            if (check(records[i].second)) {
                ans.insert(records[i].second);
            }
        }
        return {ans.begin(), ans.end()};
    }
};
```

Time complexity: O(N log N * nameLength),
space complexity: O(N).

## 1605. Find Valid Matrix Given Row and Column Sums

Greedy. For each position, choose the smaller of the row sum and column sum. It can be proven that this will always produce a valid solution in the end.

```cpp
class Solution {
public:
    vector<vector<int>> restoreMatrix(vector<int>& rowSum, vector<int>& colSum) {
        const int rows = rowSum.size(), cols = colSum.size();
        vector<vector<int>> ans(rows, vector<int>(cols, 0));
        for (int i = 0; i < rows; ++i) {
            for (int j = 0; j < cols; ++j) {
                ans[i][j] = min(rowSum[i], colSum[j]);
                rowSum[i] -= ans[i][j];
                colSum[j] -= ans[i][j];
            }
        }
        return ans;
    }
};
```

Time complexity: O(rows * cols),
space complexity: O(rows * cols).

## 1606. Find Servers That Handled Most Number of Requests

Use a `set` to maintain the available servers. Use a `priority_queue` to maintain when servers become idle, and update the `set`.

```cpp
class Solution {
public:
    vector<int> busiestServers(int k, vector<int>& arrival, vector<int>& load) {
        set<int> availble;
        for (int i = 0; i < k; ++i) {
            availble.insert(i);
        }
        using pii = pair<int, int>;
        priority_queue<pii, vector<pii>, greater<pii>> pq;
        vector<int> cnt(k, 0);
        for (int i = 0; i < arrival.size(); ++i) {
            const int time = arrival[i];
            while (!pq.empty() && pq.top().first <= time) {
                availble.insert(pq.top().second);
                pq.pop();
            }
            if (availble.empty()) continue;
            const int j = i % k;
            auto it = availble.lower_bound(j);
            if (it == availble.end()) {
                it = availble.begin();
            }
            ++cnt[*it];
            pq.push({arrival[i] + load[i], *it});
            availble.erase(it);
        }
        const int maxRequests = *max_element(cnt.begin(), cnt.end());
        vector<int> ans;
        for (int i = 0; i < cnt.size(); ++i) {
            if (cnt[i] == maxRequests) {
                ans.push_back(i);
            }
        }
        return ans;
    }
};
```

Time complexity: O(arrival.size() * k),
space complexity: O(k).
