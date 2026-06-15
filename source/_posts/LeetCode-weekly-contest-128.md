---
title: LeetCode weekly contest 128
date: 2019-03-17 15:50:49
tags:
- Competitive Programming
categories:
- LeetCode
---

The first three problems went smoothly and were solved within 30 minutes. For the last Hard problem, my thinking was quite messy, and even after one hour I still did not solve it.
This contest made me feel that it still comes down to familiarity.
Because I had done similar problems before for problems 2 and 3, I solved them quickly. The second problem even took only 2 minutes!!!

| Rank |	Name |	Score |	Finish Time | 	Q1 (2) |	Q2 (4) |	Q3 (6) |	Q4 (8)|
|--|--|--|--|--|--|--|--|
|348 / 5164|	YoungForest |	19|	1:24:03|	0:11:33(1)|	0:13:42(1)|	0:27:37	|None|

## 1012. Complement of Base 10 Integer

```cpp
class Solution {
public:
    int bitwiseComplement(int N) {
        if (N == 0) return 1;
        int ret = 0;
        for (int i = 0; i < 32; i++) {
            if ((1 << i) > N) break;
            ret += ((N & (1 << i)) == 0) ? (1 << i) : 0;
        }
        
        return ret;
    }
};
```

Time complexity: O(log N), proportional to the number of digits.
Space complexity: O(1).

Because I did not notice the corner case `if (N == 0) return 1;`, I got one wrong answer.
My method is straightforward, with no special technique or trick. After all, it is a warm-up problem, and the time complexity is sufficient.
There are also some other ideas:
for example, find the largest `X = 1111..11` such that `X >= N`, then simply `return X - N` or `return X^N`.

## 1013. Pairs of Songs With Total Durations Divisible by 60


```cpp
class Solution {
public:
    int numPairsDivisibleBy60(vector<int>& time) {
        vector<int> hashmap(60, 0);
        int ret = 0;
        for (const int & t : time) {
            ret += hashmap[(60 - t + 60000) % 60];
            hashmap[t % 60]++;
        }
        return ret;
    }
};
```

This uses the same idea as the famous `two sum`, testing the use of a `hashmap`.
To map all values, including negative numbers, into the range under 60, I used `(60 - t + 60000) % 60`. A better approach is actually `(60 - t % 60) % 60`.

Time complexity: O(N),
space complexity: O(N).

## 1014. Capacity To Ship Packages Within D Days

A typical binary search problem. I also encountered one like this while helping my senior with ByteDance's written test over the weekend. The idea was clear, and writing it felt quite practiced.

```cpp
class Solution {
    bool possible(vector<int>& weights, int capacity, int D) {
        int day_count = 0;
        int weight_index = 0;
        for (; day_count < D; day_count++) {
            int hasLoadedWeight = 0;
            for (; weight_index < weights.size() && hasLoadedWeight + weights[weight_index] <= capacity; weight_index++) {
                hasLoadedWeight += weights[weight_index];
            }
        }
        return weight_index == weights.size();
    }
public:
    int shipWithinDays(vector<int>& weights, int D) {
        // binary search，time complexity: 50000 * log(50000 * 500), Space: O(1)
        int hi = 50000 * 500;
        int lo = 0;
        while (lo < hi) {
            // loop invariant: possible(hi) == true
            int mid = lo + (hi - lo) / 2;
            if (possible(weights, mid, D))
                hi = mid;
            else
                lo = mid + 1;
        }
        
        return hi;
    }
};
```

Time complexity: 50000 * log(50000 * 500),
space: O(1).

## 1015. Numbers With 1 Repeated Digit

I spent an hour on it and still did not solve it. Looking back, the correct idea is to count how many numbers have no repeated digits, and divide the number set into 0, 1 through `digit-1`, and `digit`. The wrong ideas were: not using permutation to simplify the counting; not analyzing the number patterns thoroughly enough. In fact, it only needs to be divided into two categories: 0 to `digit-1`, and `digit`. Because of these mistakes, although I could calculate the count by hand, the code implementation became too complex and too long, making it very hard to get right directly. In the end, I had to give up.

I referred to the solutions from [lee215](https://leetcode.com/problems/numbers-with-1-repeated-digit/discuss/256725/JavaPython-Count-the-Number-Without-Repeated-Digit) and [heqingy](https://leetcode.com/problems/numbers-with-1-repeated-digit/discuss/256866/Python-O(logN)-solution-with-clear-explanation).

```cpp
class Solution {
    int permutation(int n, int m) {
        int ret = 1;
        for (int i = 0; i < n; i++) {
            ret *= m - i;
        }
        
        return ret;
    }
public:
    int numDupDigitsAtMostN(int N) {
        vector<int> digits;
        // transform N + 1 to list. Attention: have to be N + 1, not N
        int tmp = N + 1;
        while (tmp > 0) {
            digits.push_back(tmp % 10);
            tmp /= 10;
        }
        reverse(digits.begin(), digits.end());
        int len = digits.size();
        int ret = 0;
        // the first digit is 0
        // take 3452 as an example
        // len = 4
        // compute ***, **, *
        for (int i = 1; i < len; i++) {
            ret += 9 * permutation(len - i - 1, 9);
        }
        // the first digits is not 0
        set<int> seen;
        for (int i = 0; i < len; i++) {
            // if i == 2
            // compute 34**
            for (int j = ((i == 0) ? 1 : 0); j < digits[i]; j++) {
                // if j == 2
                // compute 342*
                if (seen.find(j) == seen.end()) {
                    ret += permutation(len - 1 - i, 10 - (i + 1));
                }
            }
            // break if 344...
            if (seen.find(digits[i]) != seen.end()) break;
            seen.insert(digits[i]);
        }
        
        return N - ret;
    }
};
```

Time complexity: O(log n), the number of digits.
Space complexity: O(1).


## Postscript

I have solved 279 problems now. I hope that after solving more problems, I can reach the state where, when I see a problem, I have an idea; once I have an idea, I can write it down from memory, bug-free. How many problems does it take to reach that state? 600? 900?
No matter how many it takes, I have to keep going.
I used to have a misunderstanding about grinding problems: I was not fast enough, and I did not set aside enough focused time every day. Only when the speed is high can it really be called grinding.
Let me clarify my goal again: spend one year preparing the skills needed for interviews. Data structures and algorithms are the foundation. For the rest, including programming languages, computer architecture, operating systems, design patterns, compilers, and networking, I also need to fill them in slowly. Around this time next year, it will be time to go into battle for real.

Keep going, Forest!
