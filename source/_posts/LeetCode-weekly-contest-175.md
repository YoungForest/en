---
title: LeetCode weekly contest 175
date: 2020-02-09 15:46:26
description: A weekly review of doubled values, anagram frequencies, time buckets and exam seating, written after broken glasses disrupted the contest.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/02/09/LeetCode-weekly-contest-175/
  en: https://youngforest.github.io/en/2020/02/09/LeetCode-weekly-contest-175/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-175/en-hero.webp" alt="Forest wears taped round glasses beside doubled tokens, balanced bead ropes, time bins and an exam-seat grid with diagonal constraints" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

This week my glasses broke, so I was not in form. Dinner at home was also late, so I did not finish the problems.

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 1378 / 7826 |	YoungForest | 	7	 | 0:11:41 | 0:06:51 | 0:11:41 | null | null |


## 1346. Check If N and Its Double Exist

Use a HashMap to store the numbers seen before.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
public:
    bool checkIfExist(vector<int>& arr) {
        unordered_set<int> s;
        for (int i : arr) {
            if (s.find(i * 2) != s.end() || (i % 2 == 0 && s.find(i / 2) != s.end()))
                return true;
            s.insert(i);
        }
        return false;
    }
};
```

## 1347. Minimum Number of Steps to Make Two Strings Anagram

According to the definition of Anagram, we only need to count the differences in the frequency of each character between the two strings.

Time complexity: O(N),
space complexity: O(1).

```cpp
class Solution {
    vector<int> convert(const string& s) {
        vector<int> ans(26);
        for (char c : s) {
            ++ans[c - 'a'];
        }
        return ans;
    }
public:
    int minSteps(string s, string t) {
        auto count_s = convert(s);
        auto count_t = convert(t);
        int ans = 0;
        for (int i = 0; i < 26; ++i) {
            if (count_s[i] > count_t[i]) {
                ans += count_s[i] - count_t[i];
            }            
        }
        return ans;
    }
};
```

## 1348. Tweet Counts Per Frequency

Use TreeMap to record tweets.
Pay attention to the split points of the intervals, and find the tweets inside each interval according to those split points.

Time complexity:
- recordTweet: O(log N)
- getTweetCountsPerFrequency: O(N * log N * (endTime - startTime) / interval)
Space complexity:
- O(N)

```cpp
class TweetCounts {
    unordered_map<string, map<int, int>> string_map;
public:
    TweetCounts() {
        
    }
    void recordTweet(string tweetName, int time) {
        ++string_map[tweetName][time];
    }
    
    vector<int> getTweetCountsPerFrequency(string freq, string tweetName, int startTime, int endTime) {
        int interval = 0;
        if (freq == "hour") {
            interval = 3600;
        } else if (freq == "day") {
            interval =  3600 * 24;
        } else {
            interval = 60;
        }
        const auto& m = string_map[tweetName];
        vector<int> ans;
        int st = startTime;
        auto start_it = m.lower_bound(st);
        auto end_it = m.upper_bound(endTime);
        auto it = start_it;
        while (st <= endTime) {
            auto next_it = m.lower_bound(st + interval);
            int accu = 0;
            for (; it != next_it && it != end_it; ++it) {
                accu += it->second;
            }
            ans.push_back(accu);
            st += interval;
        }
        
        return ans;
    }
};

/**
 * Your TweetCounts object will be instantiated and called as such:
 * TweetCounts* obj = new TweetCounts();
 * obj->recordTweet(tweetName,time);
 * vector<int> param_2 = obj->getTweetCountsPerFrequency(freq,tweetName,startTime,endTime);
 */
```

## 1349. Maximum Students Taking Exam

A pretty hard problem. I learned the correct solution only after watching [Hua Hua](https://www.bilibili.com/video/av88416735)'s video, so here is a friendly recommendation.
Dynamic Programming (DP) plus state compression.

Here are several bitmask tricks:
- No students to the left or right: (x & (x >> 1)) == 0
- No student on the upper-left: (a & (b >> 1)) == 0
- Enumerate all states according to the seats: state_enum = state & (state_enum - 1)

Time complexity: O(m * 2 ^ n * 2 ^ n),
space complexity: O(m * 2 ^ n) -> O(2 ^ n).

```cpp
class Solution {
    bool left_and_right(int a) {
        return (a & (a >> 1)) == 0;
    }
    bool ok(int a, int b) {
        return left_and_right(b) && left_and_right(b) && (a & (b >> 1)) == 0 && (b & (a >> 1)) == 0;
    }
public:
    int maxStudents(vector<vector<char>>& seats) {
        const int m = seats.size();
        const int n = seats[0].size();
        
        vector<vector<int>> dp(m + 1, vector<int> (1 << n));
        int last_state = 0;
        
        for (int row = 0; row < m; ++row) {
            auto& last_row = dp[row];
            auto& current_row = dp[row + 1];
            int state = 0;
            for (int col = 0; col < n; ++col) {
                state |= seats[row][col] == '.' ? (1 << col) : 0;
            }
            for (int last_state_enum = last_state; ; last_state_enum = last_state & (last_state_enum - 1)) {
                for (int state_enum = state; ; state_enum = state & (state_enum - 1)) {
                    if (ok(last_state_enum, state_enum)) {
                        current_row[state_enum] = max(current_row[state_enum], last_row[last_state_enum] +  __builtin_popcount(state_enum));
                    }
                    if (state_enum == 0) {
                        break;
                    }
                }
                if (last_state_enum == 0) {
                    break;
                }
            }
            
            last_state = state;
        }
        
        return *max_element(begin(dp[m]), end(dp[m]));
    }
};
```
