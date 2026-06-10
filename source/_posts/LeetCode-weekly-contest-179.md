---
title: LeetCode Weekly Contest 179
date: 2020-03-08 13:54:54
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 539 / 6242 |	YoungForest | 18 | 	1:09:53 | 0:05:43 | 0:13:09 | 0:24:01 | 1:04:53  1 |

## 1374. Generate a String With Characters That Have Odd Counts

If `n` is even, use one `a` and fill the rest with `b`;
if `n` is odd, use all `a`.

Time complexity: O(n),
space complexity: O(n).

When writing code before, I never paid too much attention to checking input validity. That is because LeetCode itself constrains the input. But in real interviews, interviewers sometimes care about your assumptions and checks for input, since input in actual production is always unpredictable.

```cpp
class Solution {
public:
    string generateTheString(int n) {
        if (n <= 0) return "";
        string ans;
        if (n % 2 == 1) {
            ans.append(n, 'a');
        } else {
            ans.push_back('a');
            ans.append(n - 1, 'b');
        }
        return  ans;
    }
};
```

## 1375. Bulb Switcher III

Observation: blue bulbs are always a continuous prefix, which means the blue state can be represented by the highest blue index, and this index is monotonically non-decreasing. So we only need to compare whether it is equal to the maximum index of a turned-on bulb. Each time a bulb is turned on, try to increase this `blue_index`.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
public:
    int numTimesAllBlue(vector<int>& light) {
        int max_turn_on_index = -1;
        int blue_index = -1;
        const int n = light.size();
        vector<bool> turn_on(n);
        int ans = 0;
        for (int i : light) {
            turn_on[i - 1] = true;
            max_turn_on_index = max(max_turn_on_index, i - 1);
            while (blue_index + 1 < n && turn_on[blue_index +  1] == true) {
                ++blue_index;
            }
            if (blue_index == max_turn_on_index)
                ++ans;
        }
        return ans;
    }
};
```

## 1376. Time Needed to Inform All Employees

A typical DFS. Each time, find the subordinate that takes the longest time to finish informing.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
public:
    int numOfMinutes(int n, int headID, vector<int>& manager, vector<int>& informTime) {
        unordered_map<int, vector<int>> graphs;
        for (int i = 0; i < manager.size(); ++i) {
            graphs[manager[i]].push_back(i);
        }
        if (graphs[-1].empty()) return 0;
        int head = graphs[-1][0];
        function<int(int)> dfs = [&](int index) -> int {
            if (graphs[index].empty())
                return 0;
            else {
                int ans = 0;
                for (int subordinate : graphs[index]) {
                    ans = max(ans, dfs(subordinate));
                }
                return ans + informTime[index];
            }
        };
        
        return dfs(head);
    }
};
```

## 1377. Frog Position After T Seconds

Also DFS. One thing to note is that we are looking for the position after exactly `t` seconds, so it is possible to pass through `target`, in which case the probability is 0.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
public:
    double frogPosition(int n, vector<vector<int>>& edges, int t, int target) {
        unordered_map<int, set<int>> graphs;
        for (const auto& e : edges) {
            graphs[e[0]].insert(e[1]);
            graphs[e[1]].insert(e[0]);
        }
        graphs[1].insert(0);
        int ans = 0;
        unordered_set<int> visited;
        visited.insert(0);
        function<bool(int,int,int)> dfs = [&](int index, int probability, int time) -> bool {
            visited.insert(index);
            if (time > t)
                return false;
            if (index == target) {
                if (time == t)
                    ans = probability;
                else {
                    if (graphs[index].size() <= 1)
                        ans = probability;
                    else
                        ans = 0;
                    // cout << ans << " " << probability << endl;
                }
                return true;
            } else {
                for (int neighbor : graphs[index]) {
                    if (visited.find(neighbor) == visited.end())
                        if (dfs(neighbor, probability * (graphs[index].size() - 1), time + 1))
                            return true;
                }
                return false;
            }
        };
        dfs(1, 1, 0);
        // cout << ans << endl;
        return ans > 0 ? 1.0 / ans : 0;
    }
};
```
