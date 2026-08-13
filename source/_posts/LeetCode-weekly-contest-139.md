---
title: LeetCode weekly contest 139
date: 2019-06-03 18:53:23
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/06/03/LeetCode-weekly-contest-139/
  en: https://youngforest.github.io/en/2019/06/03/LeetCode-weekly-contest-139/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (6) |	Q4 (8)|
|--|--|--|--|--|--|--|--|
| 855 / 3985 |	YoungForest | 10 | 1:03:50 | 0:53:00 | 1:03:50 | solved after the contest  | null |

When I got up on Sunday, it was already past 11, so I joined the contest 40 minutes late. I solved the first two problems smoothly. I took some detours on the third problem and only solved it after the contest. If there had been enough time, solving the third problem should not have been an issue.

## 1071. Greatest Common Divisor of Strings

Intuition:
This problem is equivalent to finding the greatest common divisor of two numbers.
The length of `Greatest Common Divisor` must be equal to the greatest common divisor or 0.
A simple proof is as follows:
Suppose the answer length is x. Since `str1` is composed of x, x must be a divisor of `str1.length`. Similarly, it is also a divisor of `str2.length`.
If x is not the greatest common divisor but can form both `str1` and `str2`, then the greatest common divisor must also be able to form both `str1` and `str2`.

Time complexity: `O(log N)`
Space complexity: `O(N)`

```cpp
class Solution {
    int gcd(int a, int b) {
        if (b > a) {
            swap(a, b);
        }
        while (b > 0) {
            auto temp = b;
            b = a % b;
            a = temp;
        }
        return a;
    }
public:
    string gcdOfStrings(string str1, string str2) {
        int len1 = str1.size();
        int len2 = str2.size();
        auto common_len = gcd(len1, len2);
        auto t1 = str1.substr(0, common_len);
        auto t2 = str2.substr(0, common_len);
        return t1 == t2 ? t1 : "";
    }
};
```

## 1072. Flip Columns For Maximum Number of Equal Rows
Intuition:
Find the maximum number of complementary rows.

Time complexity: `O(matrix.length * matrix[0].length)`
Space complexity: `O(matrix.length * matrix[0].length)`

```cpp
class Solution {
public:
    int maxEqualRowsAfterFlips(vector<vector<int>>& matrix) {
        unordered_map<string, int> count;
        int ret = 0;
        for (const auto& row : matrix) {
            string right, complete;
            for (int item : row) {
                right.push_back(item + '0');
                complete.push_back(((~item) & 1) + '0');
            }
            ++count[right];
            ++count[complete];
            ret = max({ret, count[right], count[complete]});
        }
        return ret;
    }
};
```

## 1073. Adding Two Negabinary Numbers

Intuition: 
Imitate binary addition. The difference is that the carry may be -1, 1, or 0.

Time complexity: `O(max(arr.size(), arr.size()))`
Space complexity: `O(max(arr.size(), arr.size()))`

```cpp
class Solution {
    void update(int& digit, int& carry) {
        if (digit == -1) {
            digit = 1;
            carry = 1;
        } else if (digit == 2) {
            carry = -1;
            digit = 0;
        } else if (digit == 3) {
            carry = -1;
            digit = 1;
        } else {
            carry = 0;
        }
    }
public:
    vector<int> addNegabinary(vector<int>& arr1, vector<int>& arr2) {
        int carry = 0;
        vector<int> ret;
        int i, j;
        for (i = arr1.size() - 1, j = arr2.size() - 1; i >= 0 && j >= 0; --i, --j) {
            int digit = arr1[i] + arr2[j] + carry;
            update(digit, carry);
            ret.push_back(digit);
        }
        for (; i >= 0; --i) {
            int digit = arr1[i] + carry;
            update(digit, carry);
            ret.push_back(digit);
        }
        for (; j >= 0; --j) {
            int digit = arr2[j] + carry;
            update(digit, carry);
            ret.push_back(digit);
        }
        if (carry == 1)
            ret.push_back(1);
        else if (carry == -1) {
            ret.push_back(1);
            ret.push_back(1);
        }
        while(ret.size() > 1 && ret.back() == 0)
            ret.pop_back();
        reverse(ret.begin(), ret.end());
        
        return ret;
    }
};
```

## 1074. Number of Submatrices That Sum to Target
