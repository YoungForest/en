---
title: LeetCode Weekly Contest 211
date: 2020-10-19 20:12:22
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/10/19/LeetCode-weekly-contest-211/
  en: https://youngforest.github.io/en/2020/10/19/LeetCode-weekly-contest-211/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 224 / 11960 | YoungForest | 18 | 1:23:45 | 0:03:19 | 0:31:37 | 1:00:04 | 1:18:45 1 |

This week's problems were pretty good, and my own ranking was also nice. I ACed 10 minutes early. I like this kind of tense and exciting feeling. When I ACed 40 minutes early last week, I actually was not as happy as today.
Because my rankings in two consecutive weekly contests were very high, my rank in the Cruel group also rose to 15th. A long-lost highest position. Keep it up.

## 1624. Largest Substring Between Two Equal Characters

Warm-up problem. Record the first and last occurrence index of each character, then traverse and take the difference.

```cpp
class Solution {
public:
    int maxLengthBetweenEqualCharacters(string s) {
        using pii = pair<int, int>;
        vector<pii> index(26, {-1, -1});
        for (int i = 0; i < s.size(); ++i) {
            const int idx = s[i] - 'a';
            if (index[idx].first == -1) {
                index[idx].first = i;
            }
            index[idx].second = i;
        }
        int ans = -1;
        for (auto p : index) {
            ans = max(ans, p.second - p.first - 1);
        }
        return ans;
    }
};
```

Time complexity: O(s.size()),
space complexity: O(1).

## 1625. Lexicographically Smallest String After Applying Operations

The second problem was quite unpleasant. Brute force is enough, but the implementation is still rather complex. I wrote 50 lines of code and unexpectedly got it bug-free on the first try.
Because the constraint is small, 100, brute force it is.

First, find all indices that can become the start through shift. Shift itself is also a subproblem from an original LeetCode problem, and can be conveniently implemented with 3 `reverse` operations.
Then there is no need to consider the shift operation anymore; only the increment operation needs to be considered. Greedy is enough. At this point, depending on whether `b` is odd or even, there may be 2 or 1 positions that need to be incremented.
Finally, find the minimum value reachable from all indices.


```cpp
class Solution {
public:
    string findLexSmallestString(string s, int a, int b) {
        const int n = s.size();
        vector<int> head;
        bool first = true;
        for (int i = 0; ; ++i) {
            const int condidate = ((n - i * b) % n + n) % n;
            if (condidate == 0) {
                if (!first) break;
                first = false;
            }
            head.push_back(condidate);
        }
        string ans(n, '9');
        auto shiftK = [&](string& a, const int k) -> void {
            // shift left k unit
            reverse(a.begin(), a.end());
            auto it = a.begin() + k;
            reverse(a.begin(), it);
            reverse(it, a.end());
        };
        auto minIncrease = [&](const int x) -> int {
            map<int, int> reach;
            int i = x;
            int ans = 0;
            while (reach.find(i) == reach.end()) {
                reach[i] = ans;
                i = (i + a) % 10;
                ans += a;
            }
            return reach.begin()->second;
        };
        auto fixHead = [&](const int d) -> string {
            string ans = s;
            shiftK(ans, d);
            if (b % 2 != 0) {
                const int increase = minIncrease(ans[0] - '0');
                for (int i = 0; i < n; i += 2) {
                    ans[i] = '0' + ((ans[i] - '0' + increase) % 10);
                }
            }
            const int increase = minIncrease(ans[1] - '0');
            for (int i = 1; i < n; i += 2) {
                ans[i] = '0' + ((ans[i] - '0' + increase) % 10);
            }
            return ans;
        };
        for (const int d : head) {
            const string x = fixHead(d);
            if (x < ans) {
                ans = x;
            }
        }
        return ans;
    }
};
```

Time complexity: O(n * n * 10),
space complexity: O(n).

## 1626. Best Team With No Conflicts

Dynamic programming.
First sort by age and score.
`dp[i][j] = max(dp[i-1][x] for x <= j)`
where `i` represents the age position, and `j` represents the current highest score.

```cpp
class Solution {
public:
    int bestTeamScore(vector<int>& scores, vector<int>& ages) {
        map<int, multiset<int>> players;
        const int n = scores.size();
        for (int i = 0; i < n; ++i) {
            players[ages[i]].insert(scores[i]);
        }
        map<int, int> m;    // maxScore, max_sum_score
        m[0] = 0;
        for (const auto& p : players) {
            auto it = m.begin();
            int maxScore = it->second;
            for (int x : p.second) {
                while (it != m.end() && it->first <= x) {
                    maxScore = max(maxScore, it->second);
                    ++it;
                }
                m[x] = maxScore + x;
                maxScore += x;
            }
        }
        int ans = 0;
        for (const auto&  p : m) {
            ans = max(ans, p.second);
        }
        return ans;
    }
};
```

Time complexity: O(N ^ 2),
space complexity: O(N).

## 1627. Graph Connectivity With Threshold

I was misled by the Chinese server translation. The query asks whether two nodes are connected, not whether they are directly connected. The Chinese server initially translated it as directly connected and even emphasized it. My habit in every contest is to open all problems at the beginning. Afterward, except when I encounter a hard problem and refresh to see submissions and accepted counts, I almost never refresh. So when the problem statement has an issue, even if it is updated later, I do not know. LeetCode does not have a notification mechanism like Codeforces. Fortunately, a classmate in the Cruel group also encountered the same issue, got WA, and could not understand why the expected answer was correct.

This problem is worth only 6 points, and deservedly so. It is a Hard problem that I just happened to be able to solve.
Connectivity can be implemented quickly with Union-Find; just bring the template.
Then it becomes greatest-common-divisor connectivity. The dumbest method is to check pairs with `gcd`, whose time complexity is N^N and definitely TLEs.
A more clever method is to enumerate common factors (not necessarily the greatest), then connect the common factor and its multiples. This does not implement the direct-connection concept from the problem; instead it implements indirect connectivity. Direct connectivity is unnecessary, because what we need in the end is also indirect connectivity.

The time complexity here is not easy to judge. One piece of knowledge is needed:
1 + 1/2 + 1/3 + ... + 1/n ~ log n.

That is, the complexity of the harmonic series sum is approximately logarithmic.

```cpp
class Solution {
    struct UF {
        vector<int> parent;
        int count;
        UF(int n) : parent(n), count(n) {
            iota(parent.begin(), parent.end(), 0);
        }
        // A utility function to find the subset of an element i  
        int find(int x)  
        {  
            return x == parent[x] ? x : parent[x] = find(parent[x]);
        }  

        // A utility function to do union of two subsets  
        void unite(int x, int y)  
        {  
            int xset = find(x);  
            int yset = find(y);  
            if(xset != yset) 
            {  
                parent[xset] = yset;
                --count;
            }  
        }    
    };
public:
    vector<bool> areConnected(int n, int threshold, vector<vector<int>>& queries) {
        // n * log n + queries.size();
        const int q = queries.size();
        vector<bool> ans;
        ans.reserve(q);
        UF uf(n + 1);
        for (int i = threshold + 1; i <= n; ++i) {
            for (int j = i + i; j <= n; j += i) {
                uf.unite(i, j);
            }
        }
        auto check = [&](const int a, const int b) -> bool {
            return uf.find(a) == uf.find(b);
        };
        for (const auto& x : queries) {
            ans.push_back(check(x[0], x[1]));
        }
        return ans;
    }
};
```

Time complexity: O(n log n + queries.length),
space complexity: O(n).

## Postscript

This week I was busy with CRs and UTs needed for internship conversion, as well as the first draft of my master's thesis due on the 28th. I have been working overtime continuously. I estimate it will take another two weeks, and only after submitting the thesis a week later will I get a chance to rest. Come on, YoungForest!
