---
title: LeetCode Weekly Contest 189
date: 2020-05-17 18:47:50
description: A weekly review of interval counting, stable length sorting, set containment and angular disk coverage, plus a reflection on ranking luck.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/05/17/LeetCode-weekly-contest-189/
  en: https://youngforest.github.io/en/2020/05/17/LeetCode-weekly-contest-189/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-189/en-hero.webp" alt="A moment of light crosses student intervals, blank cards reorder by length, transparent sets test containment and a rotating disk covers the densest points" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 484 / 13036|	YoungForest | 19 | 1:27:45 | 0:11:20 | 0:19:20  1 | 0:08:48 |  1:22:45 |

The last problem this week was a classic computational geometry problem and was not easy to write. But the acceptance rate was still quite high, probably because the test cases were relatively weak.

My ranking in the Cruel group dropped to 32, a 100% decline. The feeling I had before that I had become stronger was an illusion. It was because I got a good rank of 113 in Weekly Contest 186 three weeks ago, so according to the group's ranking algorithm, my ranking stayed relatively high for the next three weeks. After that best result aged out, my ranking returned to its original level, around 30. It was not that I had become stronger; I was just lucky. Also, I happened to use Python in contest 186, and Python really does have an advantage in speed contests.

## 1450. Number of Students Doing Homework at a Given Time

A warm-up problem. One pass.

Time complexity: O(N),
space complexity: O(1).

```cpp
class Solution {
public:
    int busyStudent(vector<int>& startTime, vector<int>& endTime, int queryTime) {
        int ans = 0;
        for (int i = 0; i < startTime.size(); ++i) {
            if (startTime[i] <= queryTime && queryTime <= endTime[i]) ++ans;
        }
        return ans;
    }
};
```

## 1451. Rearrange Words in a Sentence

Use the sorting function provided by the C++ STL.

Time complexity: O(N log N),
space complexity: O(N).

```cpp
class Solution {
    vector<pair<string,int>> splitSentence(const string& text) {
        string tmp;
        vector<pair<string,int>> stk;
        stringstream ss(text);
        int position = 0;
        while(getline(ss,tmp,' ')) {
            if (!islower(tmp[0])) tmp[0] = tolower(tmp[0]);
            stk.push_back({tmp, position});
            ++position;
        }
        return stk;
    }
public:
    string arrangeWords(string text) {
        auto split = splitSentence(text);
        sort(split.begin(), split.end(), [](const auto& a, const auto& b) -> bool {
            if (a.first.size() == b.first.size()) {
                return a.second < b.second;
            } else {
                return a.first.size() < b.first.size();
            }
        });
        string ans;
        for (const auto& word : split) {
            ans += word.first;
            ans.push_back(' ');
        }
        ans.pop_back();
        ans[0] = toupper(ans[0]);
        return ans;
    }
};
```

## 1452. People Whose List of Favorite Companies Is Not a Subset of Another List

Brute force. For each person, judge whether their list is contained by another person's list. This is `N^2`.
When checking containment, sort the company lists first and then compare them sequentially, O(m * log m + m).
`N` is the number of people, and `M` is the number of companies.

Time complexity: O(N^2 * M log M),
space complexity: O(N).

Here I used the `includes` function from STL algorithms, which can determine whether one sorted range contains another.

```cpp
class Solution {
public:
    vector<int> peopleIndexes(vector<vector<string>>& favoriteCompanies) {
        for (auto& v : favoriteCompanies) {
            sort(v.begin(), v.end());
        }
        const int n = favoriteCompanies.size();
        vector<int> ans;
        for (int i = 0; i < n; ++i) {
            bool remove = false;
            for (int j = 0; j < n; ++j) {
                if (i == j) continue;
                if (includes(favoriteCompanies[j].begin(), favoriteCompanies[j].end(), favoriteCompanies[i].begin(), favoriteCompanies[i].end())) {
                    remove = true;
                    break;
                }
            }
            if (!remove) {
                ans.push_back(i);
            }
        }
        return ans;
    }
};
```

## 1453. Maximum Number of Darts Inside of a Circular Dartboard

A classic problem: the disk partial covering problem.
You can find it with a casual online search. The principle can be referenced from [GeeksforGeeks](https://www.geeksforgeeks.org/angular-sweep-maximum-points-can-enclosed-circle-given-radius/) and this [discuss post](https://leetcode.com/problems/maximum-number-of-darts-inside-of-a-circular-dartboard/discuss/636416/c%2B%2B-O(n2logn)-angular-sweep-(with-picture)); it is the famous angular sweep.

Time complexity: O(n^2 log n)
space complexity: O(n ^ 2).


```cpp
class Solution {
    using Point = complex<double>;
    // angular sweep
    int getPointsInside(const vector<Point>& arr, const vector<vector<double>>& dis, int i, double r, int n) 
    { 
        vector<pair<double, bool> > angles; 
        for (int j=0; j<n; j++) 
        { 
            if (i != j && dis[i][j] <= 2*r) 
            { 
                double B =  acos(dis[i][j]/(2*r)); 
                double A = arg(arr[j]-arr[i]); 
                double alpha = A-B; 
                double beta = A+B; 
                angles.push_back(make_pair(alpha, false)); 
                angles.push_back(make_pair(beta, true)); 
            } 
        } 
        sort(angles.begin(), angles.end()); 
        int count = 1, res = 1; 
        vector<pair<double, bool> >::iterator it; 
        for (it=angles.begin(); it!=angles.end(); ++it) 
        { 
            if (!(*it).second) 
                count++; 
            else
                count--; 

            if (count > res) 
                res = count; 
        } 

        return res; 
    } 
public:
    int numPoints(vector<vector<int>>& points, int r) {
        const int n = points.size();
        vector<Point> arr;
        arr.reserve(n);
        for (const auto& p : points) {
            arr.emplace_back(p[0], p[1]);
        }
        vector<vector<double>> dis(n, vector<double>(n, 0));
        for (int i=0; i<n-1; i++) 
            for (int j=i+1; j<n; j++) 
                dis[i][j] = dis[j][i] = abs(arr[i]-arr[j]); 
        int ans = 0; 
        for (int i=0; i<n; i++) 
            ans = max(ans, getPointsInside(arr, dis, i, r, n));
        return ans; 
    }
};
```
