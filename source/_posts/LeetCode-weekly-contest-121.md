---
title: LeetCode weekly contest 121
date: 2019-01-27 16:05:48
description: Travel-day contest and phone-interview notes on alternating strings, time maps, ticket costs, bitwise triples and communicating under pressure.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/01/27/LeetCode-weekly-contest-121/
  en: https://youngforest.github.io/en/2019/01/27/LeetCode-weekly-contest-121/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-121/en-hero.webp" alt="Forest regains focus at a train worktable with alternating beads, time drawers, route passes and three layered bit plates" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

Today I went home for vacation, with a train at 3:30 p.m. But I still squeezed out time from a busy schedule to join the regular weekly contest. The result was poor because I was not focused enough while going home. I only solved the warm-up problem. The second problem TLEed, and in the end it could have passed by changing one variable to a reference, which was also a lesson: use references whenever possible. I did not have enough time to finish the third problem, though after getting on the train in the afternoon, with no distractions, I finally completed it independently. The idea had actually been correct from the beginning; I just did not have time to debug the details. As for the fourth problem, I did not even have time to finish reading the statement.

## 984. String Without AAA or BBB

Intuition: since consecutive `a` or `b` cannot appear, we can try to directly construct a valid string. The character with more remaining count appears twice, then we insert one of the character with fewer remaining count. When the remaining counts become equal, each character only needs to appear once at a time.

```cpp
class Solution {
public:
    string strWithout3a3b(int A, int B) {
        string result;
        char large, small;
        int bigger, smaller;
        if (A > B) {
            large = 'a';
            small = 'b';
            bigger = A;
            smaller = B;
        } else {
            large = 'b';
            small = 'a';
            bigger = B;
            smaller = A;
        }
        for (; bigger > 0 && smaller > 0 && bigger > smaller; bigger-=2, smaller--) {
            result.push_back(large);
            result.push_back(large);
            result.push_back(small);
        }
        for (; bigger > 0 && smaller > 0; bigger--, smaller--) {
            result.push_back(large);
            result.push_back(small);
        }
        while (bigger > 0) {
            result.push_back(large);
            bigger--;
        }
        while (smaller > 0) {
            result.push_back(small);
            small--;
        }
        
        return result;
    }
};
```

## 981. Time Based Key-Value Store

Intuition:
Use a hashmap to store `<key, vector<pair<int, string>> value>`.
Each time we find the vector corresponding to a key, use binary search `upper_bound` on the first value of the pair, the timestamp.

Note: use a reference. Never store `hashmap[key]` in an ordinary variable.

```cpp
class TimeMap {
private:
    /** Intution:
        使用hashmap存储<key, vector<pair<int, string>> value>,
        每次查找到key对应的vector后，针对pair的第一个值使用二分查找upper_bound timestamp
    */
    unordered_map<string, vector<pair<int, string>>> hashmap;
    class Cmp {
        public:
        bool operator() (pair<int, string> a, pair<int, string> b) {
            return a.first < b.first;
        }
    };
public:
    /** Initialize your data structure here. */
    TimeMap() {
        
    }
    
    void set(string key, string value, int timestamp) {
        hashmap[key].push_back({timestamp, value});
    }
    
    string get(string key, int timestamp) {
        auto& values = hashmap[key];    // 注意：用引用，千万不要用普通变量存储
        if (values.size() == 0) return "";
        auto val = upper_bound(values.begin(), values.end(), make_pair(timestamp, ""), Cmp());
        if (val == values.begin()) return "";
        return (--val)->second;
    }
};

/**
 * Your TimeMap object will be instantiated and called as such:
 * TimeMap* obj = new TimeMap();
 * obj->set(key,value,timestamp);
 * string param_2 = obj->get(key,timestamp);
 */
```

## 983. Minimum Cost For Tickets

Intuition: for an optimization problem where the optimal solution depends on subproblem solutions, DP comes to mind first.
Optimal substructure: `f(x) = min(f(x-1) + cost[0], f(x-7) + cost[1], f(x-30) + cost[2])`,
where `f(x)` represents the minimum cost when the day is `x`. Since `x` may not exist in `days`, in that case `f(x)` represents the minimum cost for the day just greater than `x`.

```cpp
class Solution {
    int getDp(vector<int>& days, vector<int>& dp, int day) {
        auto result = upper_bound(days.begin(), days.end(), day);
        // cout << day << " " << int(result - days.begin()) << " " << endl;
        if (result == days.begin())
            return 0;
        else {
            // cout << "return : " << *(result-1) << endl;
            // cout << "return : " << dp[0] << endl;
            return dp[result - days.begin() - 1];
        }
    }
public:
    int mincostTickets(vector<int>& days, vector<int>& costs) {
        vector<int> dp(days.size(), 0);
        for (int i = 0; i < days.size(); i++) {
            int day = days[i];
            dp[i] = getDp(days, dp, day - 1) + costs[0];
            dp[i] = min(dp[i], getDp(days, dp, day - 7) + costs[1]);
            dp[i] = min(dp[i], getDp(days, dp, day - 30) + costs[2]);
            // cout << "dp : " << i << " " << dp[i] << endl;
        }
        
        return dp[dp.size() - 1];
    }
};
```

## 982. Triples with Bitwise AND Equal To Zero

The brute-force method uses three nested loops to enumerate all combinations and check whether the bitwise AND equals 0. Time complexity: O(n^3).
The result was TLE.

Possible optimizations:

1. First compute all combinations of `A[i] & A[j]`, then search for possible `A[k]` values according to the missing 0 bits, and check whether `A[k]` exists.
Time complexity: O(n^2 * 2^16)
The result was TLE.

```cpp
class Solution {
    int search(unordered_map<int, int>& hashtable, vector<int>& add, int level, int base) {
        if (level == add.size()) return hashtable[base];
        return search(hashtable, add, level + 1, base) + search(hashtable, add, level + 1, base + add[level]);
    }
    int getComplementCount(unordered_map<int, int>& hashtable, int a) {
        vector<int> add;
        for (int i = 0; i < 16; i++) {
            if (!((a >> i) & 1)) {
                // 如果第i位为0
                add.push_back(1 << i);
            }
        }
        
        // cout << a << " : ";
        // for (auto i: add) {
        //     cout << i << " ";
        // }
        // cout << endl;
        return search(hashtable, add, 0, 0);
    }
public:
    int countTriplets(vector<int>& A) {
        unordered_map<int, int> hashtable;
        for (auto a: A) {
            hashtable[a]++;
        }
        int result = 0;
        for (auto a: A) {
            for (auto b: A) {
                result += getComplementCount(hashtable, a & b);
            }
        }
        
        return result;
    }
};
```

2. After finding possible `A[k]` values each time, save the result. The next time the same `A[i] & A[j]` appears, it can be used directly.
Time complexity: O(n^3).
The result was AC, because many `A[i] & A[j]` values repeat, so the actual runtime is closer to Omega(n^2).

```cpp
class Solution {
public:
    int countTriplets(vector<int>& A) {
        unordered_map<int, int> hashtable;
        int result = 0;
        for (auto a: A) {
            for (auto b: A) {
                int x = a & b;
                if (hashtable.find(x) == hashtable.end()) {
                    for (auto c: A) {
                        if ((c & x) == 0)
                            hashtable[x]++;
                    }
                }
                result += hashtable[x];
            }
        }
        
        return result;
    }
};
```

## Postscript

The day after this contest ended, I took Google's 1st Phone Interview at home. Because of the confidentiality agreement, I cannot share the specific problems.
I can only share some interview reflections again. Overall, the interview problems were not hard and were close to Medium. But solving two problems in 45 minutes, from thinking to discussion to coding, while the phone call quality was also poor, is genuinely difficult. In the end, I hurriedly finished the second problem and did not make it bug-free before ending the already overtime interview. I have now done around 170 algorithm problems on LeetCode, more than half of which are Medium+ difficulty. But that is still not enough for Google's interview. Excluding objective reasons, my subjective reasons for failure are:
- problem-solving and coding speed is not fast enough, and I have not formed basic patterns, or routines;
- high expectations for the interview created some nervousness;
- insufficient phone interview experience, and I prefer on-site interviews.

My tentative countermeasures:
- time every problem from now on, quantifying the time spent thinking, coding, testing, and debugging
- summarize algorithm problem types and patterns, and become very fluent with the code templates for each pattern
- use interest as motivation, since I like reading, and read more books related to programming, algorithms, and interviews to strengthen myself
- improve communication skills and practice more, using language, text, and diagrams to clearly express my ideas

Keep going, Forest!
