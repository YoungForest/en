---
title: Microsoft Suzhou Interview
date: 2018-06-01 22:54:08
tags:
- Microsoft
- Intern
categories:
- Interview
description: "A three-round Microsoft Suzhou interview review covering recursion, memory copying, tree problems, SQL, rounding, and lessons from rejection."
mathjax: true
translations:
  zh-CN: https://youngforest.github.io/2018/06/01/suzhou-microsoft-interview/
  en: https://youngforest.github.io/en/2018/06/01/suzhou-microsoft-interview/
---
Thanks to a referral from senior schoolmate @lxc, I had the opportunity to go to Microsoft Suzhou and interview for a summer internship in the O365 department. Although I failed in the end, I learned a lot from this failure.
I am summarizing it here to prepare for future internship and job hunting.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/suzhou-microsoft-interview/en-hero.webp" alt="Forest moves through three engineering benches with a narrowing recursion coil, overlapping memory blocks, tree paths, joined data cards, and a rounding balance" width="1536" height="864" decoding="async">
</figure>

<!-- more -->

## Interview Process

### First Round

- tail recursion, Fibonacci, which I did not know
- merge two lists
- determine Makefile compilation order
- implementation method and safety issues of `memcpy`

#### Optimizing Recursive Fibonacci with Tail Recursion

Everyone should be familiar with the Fibonacci sequence, a classic recursive sequence mentioned in high school math textbooks.
Its mathematical definition is:

$$
f_n = \begin{cases}
f_{n-1} + f_{n-2} & \quad \text{if } n > 2 \\
1 & \quad \text{if } n = 0, 1
\end{cases}
$$

According to the mathematical recurrence, it is easy to write a recursive Fibonacci implementation:

``` python
def fibonacci(n):
    if n == 0:
        return 0
    elif n == 1:
        return 1
    else:
        return fibonacci(n-1) + fibonacci(n-2)
```

According to how function calls work, each call needs to recursively call the function twice. When the argument is `n`, the number of stack frames needed is O(2^n).
Tail recursion can be used to optimize this problem.
Tail recursion is a recursive form of [tail call](https://en.wikipedia.org/wiki/Tail_call).
My understanding of tail recursion is:
it is a way to implement loops in functional programming by using function calls.
So first, here is an iterative Fibonacci implementation with O(1) memory usage.

```python
def fibonacci(n):
    if n == 0:
        return 0
    f_n_sub_1 = 1
    f_n_sub_2 = 0
    i = n
    while i > 1:
        f_n_sub_1, f_n_sub_2 = f_n_sub_1 + f_n_sub_2, f_n_sub_1
        i -= 1
    return f_n_sub_1
```

From the iterative implementation, we can see that the whole loop needs two variables, `f_n_sub_1` and `f_n_sub_2`, to record intermediate results,
and one loop variable, `i`, to control the number of iterations, which is also the loop termination condition.
Using these variables as function arguments gives the tail-recursive Fibonacci implementation.

``` python
def fibonacci(n):
    def func(n, ret1, ret2):
        if n == 0:
            return ret1
        else:
            return func(n - 1, ret2, ret1 + ret2)
    return func(n, 0, 1)
```

#### Implementation Method and Safety Issues of `memcpy`

It also copies byte by byte. It is not, as I had assumed, copying whole blocks at a time, which would be faster.
Recalling knowledge from Computer Organization:
the CPU and Memory are connected by a bus, and each time, one clock cycle, only one word of data can be read or written.

Safety: very unsafe.
Recalling knowledge from Operating Systems: a process's memory space is mapped to physical memory.
Directly copying and pasting byte by byte from low addresses to high addresses can easily overwrite data pointed to by other pointers, destroying that data, or even overwrite the data itself.

For example, if `memcpy` copies a 200-byte pointer at address 100 to address 200, it will first overwrite part of its own data, addresses 200-300, and then later copies will all be wrong. This was the interviewer's explanation, but I later found that this point is not necessarily true. Afterward I checked some standard library implementations of `memcpy`; in this situation, they may copy from back to front to avoid overwriting the data to be copied.
[GCC without protection](https://github.com/gcc-mirror/gcc/blob/master/libgcc/memcpy.c)
[Implementation with protection](https://blog.csdn.net/laoyang360/article/details/8020409)

#### Interviewer's Advice

When writing code during an interview, in Notepad, on paper, or on a whiteboard,
the ease or convenience is:
whiteboard > paper > Notepad.

After being told the interview question, first think clearly about the initial conditions and concrete requirements. If something is unclear, be sure to ask the interviewer. Do not start writing immediately.
Otherwise, if you later discover ambiguity and ask again, it is already late.

#### Other Takeaways

When I have time, I must properly read SICP. Tail recursion in functional programming is discussed there.
The interviewer had probably read it too.

### Second Round

- find the maximum depth of a tree
- maximum-sum chain
- maximum-sum chain that can turn
- rearrange an array to maximize weighted value, which I did not know

#### Other Takeaways

ACM contestants have a natural advantage in interviews.
This interviewer may have done ACM as an undergraduate, because he said the "array weight" problem was one he had designed himself while in school.

p.s. The interviewer said he had not graduated long ago either.

### Third Round

After going to Suzhou for the interview on Tuesday, I received the result on Friday, and they scheduled a video interview for the following Tuesday.
To be honest, I was a little nervous before the video interview. Also, because something happened at school, I only learned in the morning that there would be a video interview that afternoon.
In the rush, I did not nap or rest well. I was especially sleepy before the interview.
Fortunately, when the real interview started, adrenaline rose and I was no longer sleepy.

The third-round interviewer was also very nice.
Hearing that I was familiar with SQL, he gave me an SQL problem.

It was similar to an academic affairs management system. There were three tables: students, with student ID and name; courses, with course ID and course name; and enrollments, with student ID, course ID, and grade.
Query each student's math grade and Chinese grade, with the result as name, math grade, Chinese grade.
At first, we assumed every student had selected both math and Chinese. Later, this assumption was removed, testing outer join.

#### The Second Problem

The second problem tested one [IEEE rounding implementation](https://en.wikipedia.org/wiki/IEEE_754#Rounding_rules).

First, the interviewer listed some numbers and asked me to observe the pattern.
Under his guidance, I guessed it: round down at four, round up at six, and when it is five, look at the next digit.
Then I implemented this `ieee_round` in C.

<figure class="editorial-illustration">
  <img src="/en/images/ai/suzhou-microsoft-interview/en-learning-loop.webp" alt="After a blank door closes, Forest sends rejected puzzle pieces through a magnifier and feedback loop to rebuild a clearer and sturdier solution" width="1536" height="864" loading="lazy" decoding="async">
</figure>

## Result

No Hire.

> Unfortunately, the Lead interviewer finally gave a No Hire. He thinks your coding ability is not solid. Your advantage is strong thinking ability; your weaknesses are that you are not good at presenting your thinking or asking questions, and you cannot obtain new solutions from mistakes. You need to strengthen this in the future.
>
> I hope you keep working hard during graduate school, and you are welcome to join Microsoft Suzhou again.

The Lead interviewer's judgment was quite accurate, and the advice was sharp and to the point. I will keep working hard and reach Microsoft's requirements as soon as possible.

Besides that, the train ticket reimbursement that had been promised at the beginning was not given in the end, so I also lost 1200 RMB financially.
