---
title: LeetCode Weekly Contest 241
date: 2021-05-16 18:42:19
tags:
- Competitive Programming
categories:
- LeetCode
---

Both this week's weekly contest and biweekly contest went badly. Time to start the Cruel Coding check-in journey.

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 717 / 11572 | YoungForest | 12 | 	0:23:51 | 0:05:35 | 0:17:33 | 0:23:51 | null |

<!-- more -->

## 1863. Sum of All Subset XOR Totals

A warm-up problem. Brute-force backtracking, enumerating all subsets.

```cpp
class Solution {
public:
    int subsetXORSum(vector<int>& nums) {
        int ans = 0;
        function<void(const int, const int)> backtracking = [&](const int i, const int current) -> void {
            if (i == nums.size()) {
                ans += current;
                return;
            } else {
                // use this
                backtracking(i + 1, current ^ nums[i]);
                // not use this
                backtracking(i + 1, current);
            }
        };
        backtracking(0, 0);
        return ans;
    }
};
```

Time complexity: O(2^n),
space complexity: O(n).

## 1864. Minimum Number of Swaps to Make the Binary String Alternating

First count the number of `'0/1'` characters and see whether an alternating string can be formed.

Then try the two alternating patterns: 1 first / 0 first.

The key is that only the number of mismatches matters; there is no need to consider exactly how to swap.

```cpp
class Solution {
public:
    int minSwaps(string s) {
        int ans = s.size();
        vector<int> cnt(2, 0);
        for (char c : s) {
            ++cnt[c - '0'];
        }
        if (abs(cnt[0] - cnt[1]) > 1) return -1;
        for (char first : "01"s) {
            int current = 0;
            if (cnt.at(first - '0') + 1 == cnt.at('1' - first)) continue;
            for (int i = 0; i < s.size(); i += 2) {
                if (s[i] != first) {
                    ++current;
                }
            }
            ans = min(ans, current);
        }
        return ans;
    }
};
```

One thing to note:

```cpp
for (char first : "01"s)
```

must not be written as:

```cpp
for (char first : "01")
```

because the former is a string literal of type `std::string`, while the latter is a C-style static string and has a `\n` terminator.


## 1865. Finding Pairs With a Certain Sum

First observe the data scale. `nums1.length` is small, while `nums2.length` is large. So consider iterating over the small one and optimizing the large one with a `hash`.

Use a reverse hashtable to record the mapping from `nums2` value -> index.

Time complexity:

- Construction: O(nums2.length),
- add: O(1),
- count: O(nums1.length).

Space complexity: O(nums2.length).


```cpp
class FindSumPairs {
    vector<int> nums1, nums2;
    unordered_map<int, unordered_set<int>> m;
public:
    FindSumPairs(vector<int>& _nums1, vector<int>& _nums2): nums1(move(_nums1)), nums2(move(_nums2)) {
        for (int i = 0; i < nums2.size(); ++i) {
            m[nums2[i]].insert(i);
        }
    }
    
    void add(int index, int val) {
        // O(1)
        m[nums2[index]].erase(index);
        nums2[index] += val;
        m[nums2[index]].insert(index);
    }
    
    int count(int tot) {
        // O(nums1.length)
        int ans = 0;
        for (int i : nums1) {
            auto it = m.find(tot - i);
            if (it != m.end()) {
                ans += it->second.size();
            }
        }
        return ans;
    }
};

/**
 * Your FindSumPairs object will be instantiated and called as such:
 * FindSumPairs* obj = new FindSumPairs(nums1, nums2);
 * obj->add(index,val);
 * int param_2 = obj->count(tot);
 */
```


## 1866. Number of Ways to Rearrange Sticks With K Sticks Visible

For this problem, I only thought of an O(N^3) solution.

Use Dynamic Programming. The state transition is:

```python
ans = 0
for j in range(k - 1, i):
    ans = (ans + dp(j, k - 1) * f2(i-1, j)) % MOD
return ans
```

That is, consider sticks from high to low, because the tallest one must be visible. Enumerate where the `i`-th stick is placed. Among the remaining `i-1` sticks, choose some to put after `i`; what follows is a counting enumeration.

From the time complexity alone, it is certain to time out. In fact, it did, even though I tried hard to optimize constants.

Below is my final TLE code.

```python
MOD = 10**9 + 7
@cache
def f(i):
    if i <= 1: return 1
    else: return (i * f(i - 1)) % MOD
@cache
def f2(a, b):
    if a == b: return 1
    else: return (a * f2(a - 1, b)) % MOD
@cache
def dp(i: int, k: int) -> int:
    # [1:i], see k woods
    # print(i, k)
    if i < k: return 0
    elif i == k: return 1
    elif k == 1:
        # put i first and other after
        return f(i - 1)
    else:
        ans = 0
        for j in range(k - 1, i):
            # put i first and [j+1, i-1] after
            # pick num woods before
            # num = i - 1 - (j + 1) + 1
            # C_i^num * num!
            # C(n,m)=n!/((n-m)!*m!)（m≤n）
            # print('add', dp(j, k - 1), (factorial(i) // factorial(i - num)))
            ans = (ans + dp(j, k - 1) * f2(i-1, j)) % MOD
        return ans
class Solution:
    def rearrangeSticks(self, n: int, k: int) -> int:
        return dp(n, k)
```

I referred to some solutions, and indeed my recurrence was wrong.

In fact, this formula can be derived from multiple perspectives:

- [Whether the last stick can be seen](https://leetcode-cn.com/problems/number-of-ways-to-rearrange-sticks-with-k-sticks-visible/solution/qia-you-k-gen-mu-gun-ke-yi-kan-dao-de-pa-0c3g/)
- [Unsigned Stirling numbers of the first kind](https://leetcode-cn.com/problems/number-of-ways-to-rearrange-sticks-with-k-sticks-visible/solution/zhuan-huan-cheng-di-yi-lei-si-te-lin-shu-2y1k/)

With a small fix to my TLE code, it is OK.

```python
class Solution:
    def rearrangeSticks(self, n: int, k: int) -> int:
        MOD = 10**9 + 7
        @cache
        def dp(i: int, k: int) -> int:
            # [1:i], see k woods
            if i < k: return 0
            elif i == k: return 1
            elif k == 0: return 0
            else:
                # last wood can be seen, it must be `i`. dp(i-1, k - 1)
                # last wood can not be seen, (i - 1) * dp(i - 1, k)
                return (dp(i-1, k - 1) + (i - 1) * dp(i - 1, k)) % MOD

        return dp(n, k)
```
