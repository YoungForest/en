---
title: LeetCode 29 Divide Two Integers
date: 2018-10-23 16:41:47
tags:
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2018/10/23/LeetCode-29-Divide-Two-Integer/
  en: https://youngforest.github.io/en/2018/10/23/LeetCode-29-Divide-Two-Integer/
---
I have not practiced LeetCode for four weeks. On one hand, the sense of urgency decreased; on the other, I lacked execution.
Recently I have another interview to prepare for: an algorithm intern interview for the video team at Xiaohongshu.
On one side, I need to look at machine learning knowledge to avoid repeating the mistakes from the Kuaishou interview; on the other, I need to review my coding ability. Sure enough, after four weeks without practicing, I can no longer even write code well. As a future programmer, how can poor coding ability be acceptable? I still need to start practicing LeetCode again in a planned way.

Description: https://leetcode.com/problems/divide-two-integers/description/
Solution: none
Difficulty: Medium

## Brute Approach

Direct brute force: replace division with subtraction. As expected, it timed out.
Time complexity: O(m), where m is the absolute value of the quotient.
Space complexity: O(1). Apart from constants, no extra space is allocated.

```java
class Solution {
    public int divide(int dividend, int divisor) {
        if (dividend == Integer.MIN_VALUE && divisor == -1) return Integer.MAX_VALUE;
        if (divisor == 1) return dividend;
        if (dividend == 0) return 0;
        
        boolean positive = true;
        if (Integer.signum(dividend) + Integer.signum(divisor) == 0) positive = false;
        
        int count = 0;
        int originalDividend = dividend;
        
        while (true) {
            dividend = positive ? dividend - divisor : dividend + divisor;
            if (Integer.signum(dividend) == 0) {
                count = positive ? count + 1 : count - 1;
                break;
            }
            if (Integer.signum(dividend) + Integer.signum(originalDividend) == 0) break;
            count = positive ? count + 1 : count - 1;
        }
        
        return count;
    }
}
```

## Simulating Long Division

- [A very good explanation and Python code](https://leetcode.com/problems/divide-two-integers/discuss/179759/Python-7-lines-(beats-99):-How-you-learned-to-divide-when-you-were-7-years-old)
- [Java Solution, explanation awful though](https://leetcode.com/problems/divide-two-integers/discuss/13417/No-Use-of-Long-Java-Solution)

```java
public class Solution {
    public int divide(int dividend, int divisor) {
		if(dividend==Integer.MIN_VALUE && divisor==-1) return Integer.MAX_VALUE;
        if(dividend > 0 && divisor > 0) return divideHelper(-dividend, -divisor);
        else if(dividend > 0) return -divideHelper(-dividend,divisor);
        else if(divisor > 0) return -divideHelper(dividend,-divisor);
        else return divideHelper(dividend, divisor);
    }
    
    private int divideHelper(int dividend, int divisor){
        // base case
        if(divisor < dividend) return 0;
        // get highest digit of divisor
        int cur = 0, res = 0;
        while((divisor << cur) >= dividend && divisor << cur < 0 && cur < 31) cur++;
        res = dividend - (divisor << cur-1);
        if(res > divisor) return 1 << cur-1;
        // 每次调用都只计算出来商里为1的一位
        return (1 << cur-1)+divide(res, divisor);
    }
}
```
