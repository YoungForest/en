---
title: LeetCode weekly contest 136
date: 2019-05-12 12:04:20
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/05/12/LeetCode-weekly-contest-136/
  en: https://youngforest.github.io/en/2019/05/12/LeetCode-weekly-contest-136/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (6) |	Q4 (8)|
|--|--|--|--|--|--|--|--|
| 220 / 4109	|	YoungForest |	15 | 0:59:43 | 0:17:07 | 0:29:36 |	0:54:43 (1) | null |

The quality of recent contests has been decent. Even the simplest warm-up problem requires careful thought. There is also a DP problem every time. DP is the type of thing where even after doing many problems, you may still fail to write a new one when you encounter it.
This time I returned to my previous level and dropped to 200+.
For this contest, it probably required finishing the first three problems within 55 minutes to enter the top 200. First, I solved problems relatively slowly. Second, I got an index issue wrong in the DP for the third problem, which caused one penalty. So unfortunately I did not enter the top 200.

## 1041. Robot Bounded In Circle

Intuition:
Execute `instructions` until the robot faces north again. Because there are only 4 directions, it takes at most 4 executions, and may also take 2 or 1.
If it is not at the origin at this point, then continuing to move will definitely make it go farther and farther. Otherwise it is a circle.
Time complexity: O(instructions.length),
Space complexity: O(1)

```cpp
class Solution {
    enum class Direction {
        north,
        south,
        west,
        east
    };
public:
    bool isRobotBounded(string instructions) {
        // 只要执行完一次instructions, 面朝北，同时不在原点，就没有圈。否则有圈
        Direction direction = Direction::north;
        int x = 0, y = 0;
        do {
            for (char c : instructions) {
                if (c == 'G') {
                    switch (direction) {
                        case Direction::north:
                            ++y;
                            break;
                        case Direction::south:
                            --y;
                            break;
                        case Direction::west:
                            --x;
                            break;
                        case Direction::east:
                            ++x;
                            break;
                    }
                } else if (c == 'L') {
                    switch (direction) {
                        case Direction::north:
                            direction = Direction::west;
                            break;
                        case Direction::south:
                            direction = Direction::east;
                            break;
                        case Direction::west:
                            direction = Direction::south;
                            break;
                        case Direction::east:
                            direction = Direction::north;
                            break;
                    }
                } else if (c == 'R') {
                    switch (direction) {
                        case Direction::north:
                            direction = Direction::east;
                            break;
                        case Direction::south:
                            direction = Direction::west;
                            break;
                        case Direction::west:
                            direction = Direction::north;
                            break;
                        case Direction::east:
                            direction = Direction::south;
                            break;
                    }
                }
                // cout << x << " " << y << " " << static_cast<int>(direction) << endl;
            }
        } while (direction != Direction::north);
        return x == 0 && y == 0;
    }
};
```

## 1042. Flower Planting With No Adjacent

Intuition: 
Coloring problem. Because a solution must exist, we can use a greedy idea. Each time, pick any available color.

Time complexity: O(N),
Space complexity: O(N).
There is a two-dimensional array and two nested loops, so isn't it `N^2`? In fact, because `in/out degree <= 3` and the number of colors is 4, the complexity of the second dimension is actually a constant.

```cpp
class Solution {
public:
    vector<int> gardenNoAdj(int N, vector<vector<int>>& paths) {
        vector<int> ret(N, 0);
        vector<vector<int>> edges(N + 1);
        vector<set<int>> remain_color(N + 1, {1, 2, 3, 4});
        for (const auto path : paths) {
            edges[path[0]].push_back(path[1]);
            edges[path[1]].push_back(path[0]);
        }
        for (int i = 1; i <= N; ++i) {
            if (remain_color[i].begin() != remain_color[i].end()) {
                auto color = *(remain_color[i].begin());
                ret[i - 1] = color;
                for (auto j : edges[i]) {
                    remain_color[j].erase(color);
                }
            }
        }
        return ret;
    }
};
```

## 1043. Partition Array for Maximum Sum

Intuition:
When you see `Array` and `Maximum`, you can think of dynamic programming.
For dynamic programming, we need to find the recursive substructure and the optimal expression.
Back to this problem, the `maxSumAfterPartitioning` for position i can be obtained from previous solutions.

Time complexity: `O(A.size() * K)`
Space complexity: `O(A.size())`

```cpp
class Solution {
public:
    int maxSumAfterPartitioning(vector<int>& A, int K) {
        // dp
        // dp[i] 表示以第i个元素结尾的数组的结果，i已经被划分到前一个子数组
        int n = A.size();
        vector<int> dp(n + 1, 0);
        for (int i = 0; i < n; ++i) {
            int max_value = A[i];
            dp[i + 1] = max_value;
            for (int j = 0; j < K && i - j >= 0; ++j) {
                max_value = max(max_value, A[i - j]);
                dp[i + 1] = max(dp[i + 1], dp[i - j] + (j + 1) * max_value);
            }
            // cout << dp[i+1] << endl;
        }
        return dp[n];
    }
};
```

## 1044. Longest Duplicate Substring

I spent the last 30 minutes thinking about this problem, and also tried a brute-force solution that I knew would TLE. Although I got lucky in my mind, it timed out as expected.
The idea of the brute-force solution is simple: shift the string to the right by one position each time, then find the same substring.
Time complexity: O(N ^ 2),
Space complexity: O(1).
Because the string length is `10^5`, an `n^2` algorithm must time out.

```cpp
class Solution {
public:
    string longestDupSubstring(string S) {
        // 暴力解法，n^2. 肯定会超时
        int global_max_begin = 0, global_max = 0;
        for (int shift = 1; shift < S.size(); ++shift) {
            int max_begin = 0, max_ = 0;
            for (int i = shift; i < S.size() && i + global_max < S.size(); ++i) {
                if (S[i] == S[i - shift]) {
                    ++max_;
                    if (max_ > global_max) {
                        global_max = max_;
                        global_max_begin = max_begin + 1;
                    }
                } else {
                    max_ = 0;
                    max_begin = i;
                }
            }
        }
        
        return S.substr(global_max_begin, global_max);
    }
};
```

In fact, this problem is a classic problem. What is a classic problem? The problem is very clear, full of keywords, the statement is short, and there is not a single unnecessary sentence. This kind of problem often has no difficulty in understanding and no story wrapper; it belongs to the original form of problems. Usually, if you know it, you know it; if you do not, you do not. Often, you can solve it if you have done it before, and cannot if you have not.

Solution 1: suffix array + LCP (longest common prefix of the suffixes) array
[kasais-algorithm](https://www.geeksforgeeks.org/%C2%AD%C2%ADkasais-algorithm-for-construction-of-lcp-array-from-suffix-array/)

```cpp
class Solution {
    // Structure to store information of a suffix 
    struct suffix 
    { 
        int index; // To store original index 
        array<int, 2> rank; // To store ranks and next rank pair 
    }; 

    // A comparison function used by sort() to compare two suffixes 
    // Compares two pairs, returns 1 if first pair is smaller 
    static constexpr auto cmp = [](const suffix& a, const suffix& b) -> bool
    { 
        return (a.rank[0] == b.rank[0])? a.rank[1] < b.rank[1]: 
            a.rank[0] < b.rank[0]; 
    };

    // This is the main function that takes a string 'txt' of size n as an 
    // argument, builds and return the suffix array for the given string 
    vector<int> buildSuffixArray(string txt)
    { 
        int n = txt.size();
        // A structure to store suffixes and their indexes 
        struct suffix suffixes[n]; 

        // Store suffixes and their indexes in an array of structures. 
        // The structure is needed to sort the suffixes alphabatically 
        // and maintain their old indexes while sorting 
        for (int i = 0; i < n; i++) 
        { 
            suffixes[i].index = i; 
            suffixes[i].rank[0] = txt[i] - 'a'; 
            suffixes[i].rank[1] = ((i+1) < n)? (txt[i + 1] - 'a'): -1; 
        } 

        // Sort the suffixes using the comparison function 
        // defined above. 
        sort(suffixes, suffixes+n, cmp); 

        // At his point, all suffixes are sorted according to first 
        // 2 characters. Let us sort suffixes according to first 4 
        // characters, then first 8 and so on 
        vector<int> ind(n); // This array is needed to get the index in suffixes[] 
        // from original index. This mapping is needed to get 
        // next suffix. 
        for (int k = 4; k < 2*n; k = k*2) 
        { 
            // Assigning rank and index values to first suffix 
            int rank = 0; 
            int prev_rank = suffixes[0].rank[0]; 
            suffixes[0].rank[0] = rank; 
            ind[suffixes[0].index] = 0; 

            // Assigning rank to suffixes 
            for (int i = 1; i < n; i++) 
            { 
                // If first rank and next ranks are same as that of previous 
                // suffix in array, assign the same new rank to this suffix 
                if (suffixes[i].rank[0] == prev_rank && 
                        suffixes[i].rank[1] == suffixes[i-1].rank[1]) 
                { 
                    prev_rank = suffixes[i].rank[0]; 
                    suffixes[i].rank[0] = rank; 
                } 
                else // Otherwise increment rank and assign 
                { 
                    prev_rank = suffixes[i].rank[0]; 
                    suffixes[i].rank[0] = ++rank; 
                } 
                ind[suffixes[i].index] = i; 
            } 

            // Assign next rank to every suffix 
            for (int i = 0; i < n; i++) 
            { 
                int nextindex = suffixes[i].index + k/2; 
                suffixes[i].rank[1] = (nextindex < n)? 
                                    suffixes[ind[nextindex]].rank[0]: -1; 
            } 

            // Sort the suffixes according to first k characters 
            sort(suffixes, suffixes+n, cmp); 
        } 

        // Store indexes of all sorted suffixes in the suffix array 
        vector<int>suffixArr; 
        for (int i = 0; i < n; i++) 
            suffixArr.push_back(suffixes[i].index); 

        // Return the suffix array 
        return suffixArr; 
    } 

    /* To construct and return LCP */
    vector<int> kasai(string txt, vector<int> suffixArr) 
    { 
        int n = suffixArr.size(); 

        // To store LCP array 
        vector<int> lcp(n, 0); 

        // An auxiliary array to store inverse of suffix array 
        // elements. For example if suffixArr[0] is 5, the 
        // invSuff[5] would store 0. This is used to get next 
        // suffix string from suffix array. 
        vector<int> invSuff(n, 0); 

        // Fill values in invSuff[] 
        for (int i=0; i < n; i++) 
            invSuff[suffixArr[i]] = i; 

        // Initialize length of previous LCP 
        int k = 0; 

        // Process all suffixes one by one starting from 
        // first suffix in txt[] 
        for (int i=0; i<n; i++) 
        { 
            /* If the current suffix is at n-1, then we don’t 
            have next substring to consider. So lcp is not 
            defined for this substring, we put zero. */
            if (invSuff[i] == n-1) 
            { 
                k = 0; 
                continue; 
            } 

            /* j contains index of the next substring to 
            be considered to compare with the present 
            substring, i.e., next string in suffix array */
            int j = suffixArr[invSuff[i]+1]; 

            // Directly start matching from k'th index as 
            // at-least k-1 characters will match 
            while (i+k<n && j+k<n && txt[i+k]==txt[j+k]) 
                k++; 

            lcp[invSuff[i]] = k; // lcp for the present suffix. 

            // Deleting the starting character from the string. 
            if (k>0) 
                k--; 
        } 

        // return the constructed lcp array 
        return lcp; 
    }

public:
    string longestDupSubstring(string S) {
        auto suffixArray = buildSuffixArray(S);
        auto ret = kasai(S, suffixArray);
        auto it = max_element(ret.begin(), ret.end());
        if (*it == 0)
            return "";
        return S.substr(*(suffixArray.begin() + distance(ret.begin(), it)), *it);
    }
};
```

Time complexity: `O(N log N)`, `N = S.size()`.
Space complexity: `O(N)`.

Another solution uses classic binary search. The tricky part is determining whether a substring has appeared before, using a base-26 encoded hashtable.
```cpp
class Solution {
    string ret;
    const int64_t prime = 288230376151711717; 
    bool compare(const string& S, int first, int second, int len) {
        for (int i = 0; i < len; ++i) {
            if (S[first + i] != S[second + i])
                return false;
        }
        return true;
    }
    pair<bool, int> possible(const string& S, int len) {
        // if the duplicate substring the length of which is len exist
        unordered_map<int64_t, vector<int>> seen;
        int64_t hash_code = 0;
        int64_t highest_weight = 1;
        for (int i = 0; i < len; ++i) {
            hash_code = (hash_code * 26 + S[i] - 'a') % prime;
            highest_weight = (highest_weight * 26) % prime;
        }
        seen[hash_code].push_back(0);
        
        for (int i = 1; i + len <= S.size(); ++i) {
            hash_code = (hash_code * 26 + S[i + len - 1] - 'a') % prime;
            hash_code = (hash_code + prime - (S[i - 1] - 'a')*highest_weight % prime) % prime;
            if (seen.find(hash_code) != seen.end()) {
                for (auto begin_index : seen[hash_code]) {
                    if (compare(S, begin_index, i, len)) {
                        return {true, i};
                    }
                }
            }
            seen[hash_code].push_back(i);
        }
        return {false, -1};
    }
public:
    string longestDupSubstring(string S) {
        // true, true, true, ..., false
        // lo, ...,                     hi
        int lo = 0, hi = S.size();
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            // cout << mid << " ";
            auto r = possible(S, mid);
            if (r.first) {
                // cout << "true" << endl;
                lo = mid + 1;
            } else {
                // cout << "false" << endl;
                hi = mid;
            }
        }
        if (lo <= 1) return "";
        auto r = possible(S, lo - 1);
        // cout << r.second << " " << lo - 1 << " " << S.size();
        return S.substr(r.second, lo - 1);
    }
};
```
Time complexity: O(N log N),
Space complexity: O(N), hashtable + return value.

The fourth problem is still very hard. Even after reading discuss, it took me half a day to reproduce the code.
The Kasai algorithm is basically copied; writing it correctly myself would still be difficult.
For the binary search method, I fell into a pit when doing base-26 encoding and forgot `character - 'a'`. After that, I spent quite a lot of time on the invariant of binary search. As *Programming Pearls* says, only 10% of programmers can write binary search correctly in one try; there are too many traps. I recommend someone else's [tutorial](
https://www.zhihu.com/question/36132386/answer/530313852). Half-open intervals, preserving invariants while shrinking intervals, and how to use `lower_bound` and `upper_bound` to complete other binary search tasks are all explained very clearly.
