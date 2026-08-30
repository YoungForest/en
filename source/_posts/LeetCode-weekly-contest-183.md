---
title: LeetCode Weekly Contest 183
date: 2020-04-07 12:33:13
description: Weekly notes on greedy subsequences, binary reduction, diverse strings and stone-game DP, alongside a long-awaited rating recovery.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/04/07/LeetCode-weekly-contest-183/
  en: https://youngforest.github.io/en/2020/04/07/LeetCode-weekly-contest-183/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-183/en-hero.webp" alt="Forest studies a greedy balance, carry-bead spiral, guarded three-colour braid and two wooden players taking stones in turns" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (4) |	Q3 (6) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 91 / 12542 |	YoungForest | 	21 | 0:39:07 | 0:09:24 |  0:15:33 |  0:29:53 | 0:39:07 |

This week was another speed contest, and a full 800 people got AK. Maybe because of the pandemic, programmers are all WFH (work from home), and the number of participants in every weekly contest has been steadily rising. Compared with when I had just returned to China, it has already doubled. My rating had been falling for about a month, and this week it finally rose again, to 2187, returning to its highest point.

## 1403. Minimum Subsequence in Non-Increasing Order

Greedy. Sort first and try to choose large values until the accumulated sum exceeds half.

Time complexity: O(N log N),
space complexity: O(N).

```cpp
class Solution {
public:
    vector<int> minSubsequence(vector<int>& nums) {
        sort(nums.begin(), nums.end(), greater<int>());
        int sumAll = accumulate(nums.begin(), nums.end(), 0);
        vector<int> ans;
        int sumCurrent = 0;
        for (int i : nums) {
            ans.push_back(i);
            sumCurrent += i;
            if (sumCurrent > sumAll / 2) {
                return ans;
            }
        }
        return ans;
    }
};
```

## 1404. Number of Steps to Reduce a Number in Binary Representation to One

Use a string to simulate the "plus one" operation until it becomes 1.
Here I used a `list` to store characters, making carry operations easier.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
public:
    int numSteps(string s) {
        list<char> l(s.begin(), s.end());
        int ans = 0;
        while (l.size() > 1) {
            if (l.back() == '1') {
                auto it = l.rbegin();
                for (;it != l.rend(); ++it) {
                    if (*it == '0') {
                        *it = '1';
                        goto endIf;
                    } else {
                        *it = '0';
                    }
                }
                l.push_front('1');
                endIf: ;
            } else {
                l.pop_back();
            }
            ++ans;
        }
        return ans;
    }
};
```

## 1405. Longest Happy String

Greedy. Each time, try to use the character with the largest remaining count.

Time complexity: O(a + b + c),
space complexity: O(a + b + c).

```cpp
class Solution {
public:
    string longestDiverseString(int a, int b, int c) {
        // greedy
        string ans;
        vector<int> count = {a, b, c};
        auto maxChar = [&](const vector<int>& mask) -> int {
            int ans = -1;
            for (int i = 0; i < count.size(); ++i) {
                if (mask[i] == 0 && (ans == -1 || count[i] > count[ans])) {
                    ans = i;
                }
            }
            return ans;
        };
        while (count[0] > 0 || count[1] > 0 || count[2] > 0) {
            int nextChar = -1;
            vector<int> mask(3, 0);
            if (ans.size() >= 2 && ans[ans.size() - 1] == ans[ans.size() - 2]) {
                mask[ans[ans.size() - 1] - 'a'] = 1;
                nextChar = maxChar(mask);
            } else {
                nextChar = maxChar(mask);
            }
            if (count[nextChar] == 0) {
                return ans;
            }
            ans.push_back('a' + nextChar);
            --count[nextChar];
        }
        return ans;
    }
};
```

## 1406. Stone Game III

DP.
`f(x)`: starting from `x`, the score the first player can get under optimal play.
Because both players execute the optimal strategy:
```
f(x) = max(剩余所有和 - f(x + 1),
剩余所有和 - f(x + 2),
剩余所有和 - f(x + 3)).
```
Alice's maximum score is `f(0)`.

Time complexity: O(N),
space complexity: O(N).

```python
class Solution:
    def stoneGameIII(self, stoneValue: List[int]) -> str:
        @lru_cache(None)
        def suffixFunction(x: int) -> int:
            if x == len(stoneValue):
                return 0
            else:
                return stoneValue[x] + suffixFunction(x + 1)
            
        @lru_cache(None)
        def f(x: int) -> int:
            if x >= len(stoneValue):
                return 0
            else:
                suffix = suffixFunction(x)
                return max(suffix - f(x + 1), suffix - f(x + 2), suffix - f(x + 3))
        alice = f(0)
        bob = suffixFunction(0) - alice
        if alice == bob:
            return 'Tie'
        elif alice > bob:
            return 'Alice'
        else:
            return 'Bob'
```

## Postscript

Today I found some contest blog posts I wrote a long time ago, probably from more than a year ago when I was preparing for a Google phone interview. Back then I was still very weak; being able to solve two problems was about it. I also failed Google's interview in the first round. The questions were just the Gas Station problem and the Lowest Common Ancestor problem, and I did not answer either very well. Looking at them now, they are not difficult problems. It is a pity that I was underprepared then, and my level was limited. One year later, facing a Google interview again, although my answer was not perfect, I still passed the first round. I started solving many algorithm problems at the end of 2018, and now I have done 900+ problems and participated in 50+ contests. My algorithm level has visibly improved, although the gap between me and the real experts is still large.

However, because of the pandemic, the application did not continue. HR only said the recruiting process had changed. After talking with group members in the Cruel LeetCode group, I learned that in the US, many New Graduate and intern recruiting processes have stopped; many offers that had already been sent were also rescinded, or changed to work from home. Surely Google China will not do the same, right?
I used to only know that the job market was getting worse every year, but I did not expect this year to be so bad. Both internships and graduation have become problems.
