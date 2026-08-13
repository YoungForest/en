---
title: LeetCode weekly contest 159
date: 2019-10-21 09:29:11
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/10/21/LeetCode-weekly-contest-159/
  en: https://youngforest.github.io/en/2019/10/21/LeetCode-weekly-contest-159/
---
## 1232. Check If It Is a Straight Line

Check whether every three consecutive points are collinear.

Time complexity: O(N),
space complexity: O(1).

```cpp
class Solution {
    bool right(const vector<int>& p1, const vector<int>& p2, const vector<int>& p3) {
        return (p1[0] - p2[0]) * (p2[1] -  p3[1]) == (p1[1] - p2[1]) * (p2[0] - p3[0]);
    }
public:
    bool checkStraightLine(vector<vector<int>>& coordinates) {
        for (int i =   0; i + 2 < coordinates.size(); ++i) {
            if (!right(coordinates[i], coordinates[i + 1], coordinates[i + 2])) {
                return  false;
            }
        }
        return true;
    }
};
```

## 1233. Remove Sub-Folders from the Filesystem

Use a HashSet to record all paths that have appeared, then traverse all paths and decompose each level of parent directories one by one, checking whether they appear in the record.

Time complexity: O(folder.size() * string.size()),
space complexity: O(folder.size() * string.size()).

```cpp
class Solution {
public:
    vector<string> removeSubfolders(vector<string>& folder) {
        unordered_set<string> memo;
        for (auto& f : folder) {
            memo.insert(f);
        }
        vector<string> ans;
        for (auto& f : folder) {
            int i = f.size() - 1;
            bool appear = false;
            while (i >= 0) {
                while (i >= 0 && f[i] != '/') {
                    --i;
                }
                auto head = f.substr(0, i);
                if (memo.find(head) != memo.end()) {
                    appear = true;
                    break;
                }
                --i;
            }
            if (!appear) {
                ans.push_back(f);
            }
        }
        return ans;
    }
};
```

## 1234. Replace the Substring for Balanced String

Sliding window.
Record all extra letters, then set up a window such that the number of occurrences of the extra letters inside the window is greater than or equal to the extra count. Then move this window while keeping that property unchanged. Record the minimum window length.

Time complexity: O(N),
space complexity: O(1).

```cpp
class Solution {
    using ll = long long;
public:
    int balancedString(string s) {
        auto charaters = {'Q', 'W', 'E', 'R'};
        unordered_map<char, ll> count;
        for (char c : s) {
            ++count[c];
        }
        ll target = s.size() / 4;
        ll window_size = 0;
        unordered_set<char> one;
        for (const auto& p : count) {
            // cout << p.first << ", " << p.second << endl;
            if (p.second > target) {
                window_size += p.second - target;
                one.insert(p.first);
            }
        }
        if (window_size == 0)
            return 0;
        ll i = 0;
        unordered_map<char, ll> one_amount;
        while (i < s.size()) {
            if (all_of(one.begin(), one.end(), [&](const auto& p) -> bool {
                return one_amount[p] >= count[p] - target;
            })) {
                break;
            }
            if (one.find(s[i]) != one.end()) {
                ++one_amount[s[i]];
            }
            ++i;
        }
        ll left = 0;
        while (left < s.size() && (one.find(s[left]) != one.end() && one_amount[s[left]] > count[s[left]] - target) || (one.find(s[left]) == one.end())) {
            --one_amount[s[left]];
            ++left;
        }
        ll ret = i - left;
        for (; i < s.size(); ++i) {
            if (one.find(s[i]) != one.end()) {
                ++one_amount[s[i]];
                while (left < s.size() && (one.find(s[left]) != one.end() && one_amount[s[left]] > count[s[left]] - target) || (one.find(s[left]) == one.end())) {
                    --one_amount[s[left]];
                    ++left;
                }
            }
            ret = min(ret, i - left + 1);
        }
        return ret;
    }
};
```

## 1235. Maximum Profit in Job Scheduling

Similar to a knapsack problem.
First sort jobs by `endTime`. Traverse each job, decide whether to include it, and update the total `profit`.
The update strategy is: according to the job's `start_time`, use binary search to find a valid `end_time`, and obtain the maximum profit if this job is included. If the maximum profit is greater than the previous maximum profit for `end_time` - because jobs have already been sorted by `end_time`, we can guarantee that the job's `end_time` is the `end_time` that obtains the maximum profit - then update `profit`.
Finally return the profit of the largest `end_time`.

Time complexity: O(N log N),
space complexity: O(N).

```
class Solution {
public:
    int jobScheduling(vector<int>& startTime, vector<int>& endTime, vector<int>& profit) {
        map<int, int> dp;
        dp[0] = 0;
        const int n = startTime.size();
        vector<tuple<int, int, int>> jobs(n);
        for (int i = 0; i < n; ++i) {
            jobs[i] = {endTime[i], startTime[i], profit[i]};
        }
        sort(jobs.begin(), jobs.end());
        for (const auto& job : jobs) {
            int cur = prev(dp.upper_bound(get<1>(job)))->second + get<2>(job);
            if (cur > dp.rbegin()->second) {
                dp[get<0>(job)] = cur;
            }
        }
        return dp.rbegin()->second;
    }
};
```
