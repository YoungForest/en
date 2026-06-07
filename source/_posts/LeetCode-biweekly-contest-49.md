---
title: LeetCode Biweekly Contest 49
date: 2021-04-04 19:52:35
tags:
- Competitive Programming
categories:
- LeetCode
---


| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 108 / 9082 | YoungForest | 18 | 1:37:36 | 0:03:39 |  0:09:01 |  0:14:28 | 1:27:36  2 |

Recently, because I relaxed my problem-solving practice, my contest level has also declined somewhat. But this is something I did deliberately. Previously, I frantically solved 1k+ problems. Later, after hitting a bottleneck, I changed to solving three problems per day: one daily problem on the China site, one daily problem on the US site, and one daily problem from the Cruel Coding group. They had points and red-packet rewards respectively. Now I am basically at zero problems per day, only continuing to participate in weekly contests to maintain feel and get some joy.

Admittedly, problem solving and contests are very fun and addictive. I have long been unable to stop.

But because I am facing the pressure of graduation and the graduation thesis, I deliberately controlled the time and investment I put into problem solving.

I do not know whether you can understand this, but when writing a thesis, anything can become a temptation. Problem solving, which I usually like a lot, becomes an even bigger temptation and escape. At root, writing papers and doing experiments are truly too painful.

It does not even have to be problem solving. Anything that stops me is enough: reading books, watching movies, browsing forums.

There is no way around it. The thesis always forces me to put my main energy into it. I am not a person with strong enough willpower.

Therefore, I deliberately kept myself away from problem solving. Tasting only the joy of weekly contests is enough.

Because of my declining level and my China-site rating being too high, 2400+, I have basically moved to the US site now. The US-site account has a rating below 2200 and still has quite a bit of room to rise. Competing there is also not too stressful.

Previously on the China site, I dropped twice in a row and almost fell out of the 2400 club.

I plan to first push my US-site rating to 2400 as well. This way I do not put all my eggs in one basket, and my rating will be more stable.

<!-- more -->

## 1812. Determine Color of a Chessboard Square

A warm-up problem. Infer the color of the corresponding coordinate from the alternating black-white pattern of the chessboard.

```cpp
class Solution {
public:
    bool squareIsWhite(string coordinates) {
        bool firstBlack = ((coordinates[0] - 'a') % 2) == 0;
        if (((coordinates[1] - '1') % 2 ) == 0) {
            return !firstBlack;
        } else {
            return firstBlack;
        }
    }
};
```

Time complexity: O(1),
space complexity: O(1).

## 1813. Sentence Similarity III

Straightforward. According to the problem statement, if `a` is similar to `b`, they need to be equal from the beginning and the end, then meet in the middle.

```python
class Solution:
    def areSentencesSimilar(self, sentence1: str, sentence2: str) -> bool:
        def similar(a, b):
            aa = a.split(' ')
            bb = b.split(' ')
            # a insert a sentence to b
            if len(aa) > len(bb): return False
            begin = 0
            while begin < len(aa) and aa[begin] == bb[begin]:
                begin += 1
            end = len(aa) - 1
            endB = len(bb) - 1
            while end >= begin and endB >= begin and aa[end] == bb[endB]:
                end -= 1
                endB -= 1
            return begin > end
            
            
        return similar(sentence1, sentence2) or similar(sentence2, sentence1)
```

Time complexity: O(sentence.length),
space complexity: O(sentence.length).

## 1814. Count Nice Pairs in an Array

`nums[i] + rev(nums[j]) == nums[j] + rev(nums[i])` can be transformed into:

`nums[i] - rev(nums[i]) == nums[j] - rev(nums[j])`.

Thus, the problem becomes counting equal pairs.

```cpp
class Solution {
    const int MOD = 1e9 + 7;
    int rev(const int x) {
        string s = to_string(x);
        reverse(s.begin(), s.end());
        return stoi(s);
    }
public:
    int countNicePairs(vector<int>& nums) {
        unordered_map<int, int> cnt;
        int ans = 0;
        for (int x : nums) {
            const int k = x - rev(x);
            ans = (ans + cnt[k]) % MOD;
            ++cnt[k];
        }
        return ans;
    }
};
```

Time complexity: O(32 * N),
space complexity: O(N).

## 1815. Maximum Number of Groups Getting Fresh Donuts

### The Wrong Greedy Version That AC'd During the Contest

Because the data scale is small, `1 <= batchSize <= 9`, `1 <= groups.length <= 30`, we can judge that the time complexity requirement is not high. It is very likely to be a backtracking-search solution. This was indeed proven true: the answers in the Discuss section were basically all `DFS + DP`.

During the contest, although I also AC'd, honestly the solution was not correct.

First, the problem can be transformed into classifying by `%batchSize`, because numbers with the same remainder are obviously equivalent.

Then group them so that the sum inside each group equals `batchSize`, and find the maximum number of groups.

I wrote a greedy algorithm, believing that we should first group one number at a time, then two numbers at a time, ..., up to `groups.length` numbers at a time, and that this grouping order was "optimal". After getting `WA`, I quickly realized that this was not the case. This grouping order was not right.

The time complexity of enumerating all grouping orders is `30!`, which is obviously not feasible. Also, there were only 10 minutes left at the time. So I tried to think that for most cases, smaller groups still fit intuition. The key was that some test cases violated it, so I tried swapping some grouping orders. Thinking that smaller counts had larger impact, I tried swapping 2 and 3, then swapping 3 and 4. Unexpectedly, it `AC`'d. Quite magical, and very lucky.

```cpp
class Solution {
public:
    int maxHappyGroups(int batchSize, vector<int>& groups) {
        vector<int> cntOrigin(batchSize, 0);
        for (int x : groups) {
            ++cntOrigin[x % batchSize];
        }
        vector<int> v(groups.size());
        iota(v.begin(), v.end(), 1);
        auto tryOne = [&]() -> int {
            auto cnt = cntOrigin;
        int ans = 0;
        auto updateAns = [&](vector<int>& useCnt) -> void {
            int minOne = numeric_limits<int>::max();
            for (int i = 0; i < useCnt.size(); ++i) {
                if (useCnt[i] == 0) continue;
                minOne = min(minOne, cnt[i] / useCnt[i]);
            }
            for (int i = 0; i < useCnt.size(); ++i) {
                if (useCnt[i] == 0) continue;
                cnt[i] -= useCnt[i] * minOne;
            }
            ans += minOne;
        };
        function<void(const int, const int, vector<int>&)> backtracking = [&](const int remain, const int s, vector<int>& useCnt) -> void {
            if (remain == 0) {
                if (s % batchSize == 0) {
                    updateAns(useCnt);
                }
                return;
            } else {
                for (int i = 0; i < batchSize; ++i) {
                    if (cnt[i] == 0) continue;
                    ++useCnt[i];
                    backtracking(remain - 1, s + i, useCnt);
                    --useCnt[i];
                }
                return;
            }
        };
        
        for (int r : v) {
            // pick r elements from 0, 1, ..., batchSize - 1
            // allow duplicate
            if (accumulate(cnt.begin(), cnt.end(), 0) < r) break;
            vector<int> useCnt(batchSize, 0);
            backtracking(r, 0, useCnt);
        }
        if (accumulate(cnt.begin(), cnt.end(), 0) > 0) {
            ++ans;
        }
        return ans;
        };
        
        int ans = tryOne();
        swap(v[2],v[3]);
        ans = max(ans, tryOne());
        return ans;
    }
};
```

Time complexity: because backtracking has many pruning branches, I am not sure what the real time complexity is. It can be considered enumerating all possible combinations and must be less than O(2^(group.size() + 1)). It is probably around O(2^9).

Space complexity: O(group.length + batchSize).

### Correct DFS + DP Version Learned After the Contest

[C++ solution from Discuss](https://leetcode.com/problems/maximum-number-of-groups-getting-fresh-donuts/discuss/1140961/C%2B%2B-Greedy-%2B-Backtracking-Video-explanation-with-and-English)

In short, each time it tries using any available group, decrementing one, and finds the maximum among them.

You can also watch [Huahua's video explanation](https://www.bilibili.com/video/BV1CU4y187tk), which is pretty good.

My implementation:

```cpp
struct VectorHasher {
  size_t operator()(const vector<int>& V) const {
    size_t hash = V.size();
    for (auto i : V)
      hash ^= i + 0x9e3779b9 + (hash << 6) + (hash >> 2);
    return hash;
  }
};
class Solution {
public:
    int maxHappyGroups(int batchSize, vector<int>& groups) {
        vector<int> cnt(batchSize, 0);
        for (int x : groups) {
            ++cnt[x % batchSize];
        }
        unordered_map<vector<int>, int, VectorHasher> memo;
        function<int(const int)> dfs = [&](const int s) -> int {
            auto it = memo.find(cnt);
            if (it != memo.end()) return it->second;
            int ans = 0;
            for (int i = 1; i < batchSize; ++i) {
                if (cnt[i] == 0) continue;
                --cnt[i];
                ans = max(ans, dfs((s + i) % batchSize) + (s == 0 ? 1 : 0));
                ++cnt[i];
            }
            return memo[cnt] = ans;
        };
        
        return cnt[0] + dfs(0);
    }
};
```

Time complexity: O(product of nonzero cnt elements * batchSize);
space complexity: O(product of nonzero cnt elements). This is actually the cache size.

In Huahua's video, he thinks the worst case occurs when `cnt` is evenly distributed. At that time:

Time complexity: O(k * (n / k) ^ k) = 2,359,296,
space complexity: O((n / k) ^ k) = 262144.

Here k = batchSize, n = groups.size().

Therefore, it can still AC in the worst case.

## Rejudge

LeetCode does not update ratings immediately after each contest. It waits three or four days. In between there is a plagiarism check and rejudge process. For some problems, because the test cases during the contest were too weak, incorrect or timeout-prone algorithms might sneak through, such as my fourth problem. I was happy for nothing; after rejudge, it indeed failed. But honestly, my own algorithm was wrong, so I cannot blame anyone else.

Rejudge only restores the truth, and it is very important for contest fairness. Although this time I was a victim of rejudge, most of the time I am a beneficiary. I also strongly support rejudge. I only hope LeetCode can improve the quality of contest problems and test cases in the future, and make post-contest judging and rating updates faster. Do not make everyone wait four or five days. The next weekly contest is almost here.

Updated result:

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 108 / 9082 | YoungForest | 18 | 1:37:36 | 0:03:39 |  0:09:01 |  0:14:28 | 1:27:36  2 |
