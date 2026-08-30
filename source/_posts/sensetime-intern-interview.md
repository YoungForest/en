---
title: SenseTime Trainee Computer Vision Intern
date: 2018-12-09 11:29:41
tags:
- sensetime
- intern
categories:
- interview
description: "Three demanding technical internship interviews, followed by a choice to value hands-on engineering and a lifetime of writing code over job-title prestige."
translations:
  zh-CN: https://youngforest.github.io/2018/12/09/sensetime-intern-interview/
  en: https://youngforest.github.io/en/2018/12/09/sensetime-intern-interview/
---
Last week I sent my resume to a classmate interning at SenseTime and scheduled an interview with HR for this Thursday. On Friday I received another call from HR to discuss the offer. I have to say, SenseTime's recruiting efficiency is really high. From another angle, this also reflects the fact that they are very short of people. Many SenseTime classmates asked me whether I had other classmates to recommend for internships.

SenseTime alumni are jokingly called the "BUAA lab," because SenseTime has recruited a large number of BUAA interns, and many full-time employees were converted directly from internships. When I was an undergraduate, almost an entire class was interning there.

During my interview process, the person who referred me was a classmate who had been interning there for a long time. The first-round interviewer was the TA from my junior-year compiler lab. The second- and third-round interviewers were also my undergraduate classmates. So it can be said that if you are a BUAA student, entering SenseTime is much easier than for students from other schools.

The group I interviewed for was the "SenseTime Research Institute Basic Technology and Tools Group," which counts as a more engineering-oriented group inside the research institute. Overall, the interview process was still very hardcore and high-energy. Several times I thought I was done, but fortunately the interviewer gave timely hints and helped me continue. Below, I will record the process of the three interviews.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/sensetime-intern-interview/en-hero.webp" alt="Forest crosses three increasingly deep technical workbenches, moving from algorithm pieces into operating-system gears, compiler pipelines, and language mechanisms" width="1536" height="864" decoding="async">
</figure>

<!-- more -->

## First Interview

The first-round interviewer was the lead of this group, who had just graduated and converted to full-time this year. I had always thought he looked familiar. Later, after talking with classmates, I remembered: wasn't this the TA who checked my compiler lab two years ago?

Because the lead was hiring for his own team, the first interview was overall very tight-paced and substantial. He asked me about career planning, project experience, and two algorithm problems in order.

Career planning was mainly related to the future internship work, from super hardcore embedded systems and operating systems, to optimizing deep learning frameworks and libraries, and then to optimizing computer vision strategies and algorithms.

For project experience, he mainly asked about my two internships and graduation project, and asked which part of my previous work and internships I was most interested in. Of course I praised all my previous internships first, then compared them and said I liked the Kuaishou internship the most. From the Q&A with the interviewer, I felt that he cared a lot about whether the candidate truly wanted to do a certain type of work and whether the candidate was genuinely interested in it.

There were two algorithm problems:

1. Given a 0/1 string, find the length of the longest substring with the same number of 0s and 1s. For example, in `'00100110011'`, the longest substring length is 10.
2. There are `1-n` street lights. Perform n operations. In the i-th operation, toggle the state of all lights whose number is a multiple of i. Initially all lights are off. For a given n, how many lights are on in the end?

Neither problem was very difficult. Although I did not solve them immediately, I eventually solved both with the interviewer's hints. My performance was neither very good nor very bad. While thinking about the solutions, I once felt that I was about to fail and embarrass myself. Fortunately, the problems themselves were not too hard, and after more thinking and attempts, I solved them. Only the first problem required hand-written code, and I quickly finished it in Python. I implemented the buckets with a 2D list, and the interviewer pointed out: wouldn't using a Map be more elegant? The second problem's solution was too simple, so there was no need to write code.

The lesson is that although my coding ability is decent and I have solved around 100 Easy LeetCode problems, I have not formed my own problem-solving process, nor have I summarized each type of problem well enough to quickly identify a suitable algorithm.

I rely more on inspiration and trial. For example, for the first problem, I was completely stuck at first. But after trying a few more times, I happened to think of computing prefix sums first, subtracting 1 on 0 and adding 1 on 1, and then the problem was solved. It is similar to some string comparison problems on LeetCode.

This inspiration-and-trial-based solution method first of all does not work every time; if my state is bad, it may fail. Second, it is unreliable. When solving a problem, I do not feel grounded, and I am constantly nervous about whether the problem can be solved successfully.

So, before facing more future interviews and dream jobs, it is necessary to form a stable and reliable problem-solving process.

## Second Interview

The second-round interviewer was my undergraduate classmate, but we did not communicate much normally. He is the more geeky type.

At the beginning, this classmate was not very talkative. He asked what was covered in the first interview and what I could write. I judged that he probably did not have much experience as an interviewer and was not very skilled at it. I relaxed a little and felt slightly happy.

But very quickly, he really used technology to teach me how to be humble. After slowly getting familiar, he also entered interview mode. He asked from operating systems, such as what happens between typing a command in the shell and the program being executed, and the process creation process, to compilers, including the compilation process and details of each step; from compiler optimization, such as loop unrolling, to CPython implementation, including global variable handling and how Python runs. Seeing that I had used Django, he also asked about framework implementation and model implementation. He asked which language I was more familiar with. I said Python, so he asked many questions about Python implementation. He asked whether I had read the code of any large Python project. I could only say, ashamed, no.

Except for the first few minutes, which were relatively relaxed, the pace of the whole interview got faster and faster. Every question went deeper and deeper until I really could not answer.

To deal with this type of interview, I can only say that **CSAPP** and **SICP** are truly important. Although I had only read a few chapters of each, I still forced myself to talk based on my shallow memory. For example, I had never understood Python's runtime process before. At first I only said it was interpreted execution. The other side asked for more detail: what is the process from start to finish? I thought Lisp is also an interpreted language, so I described the Lisp execution process I had read about long ago in SICP. After the interview, I looked up Python's execution process and was pleasantly surprised to find that it really was similar. CSAPP gives deep introductions to compilation and processes. If I had been more familiar with those two chapters, I would definitely have been more composed during the interview. Not having read the source code of large projects is also one of my weaknesses. To be honest, not many people have read the source code of many large projects. But we should aim for great engineers. Reading more source code is necessary to become a better programmer.

## Third Interview

The third-round interviewer was also my undergraduate classmate, and we were slightly more familiar. The last round was handled by a classmate from a different group and was called a cross-group interview, to prevent any group from privately hiring unqualified employees.

Since we were relatively familiar, the interview process was quite relaxed. First he asked about my project experience one by one, then asked one language question and one algorithm question.

The language question involved Python closures, specifically understanding the difference between:

```python
[lambda x: x + i for i in range(4)]
```

and

```python
def func1(i):
    return lambda x: x + i

[func1(i) for i in range(4)]
```

I had read about closures in Liao Xuefeng's Python tutorial many years ago, but only had a faint impression and rarely used them in practice. I only remembered that variable `i` sometimes changes and produces unexpected results, such as in the former case. Fortunately, with the interviewer's hints, I got through it without danger.

The algorithm problem was about DP:

Given `n <= 20, a_i < 100, integer s`, solve how many assignments of positive and negative signs make the equation hold:

$$ \sum_i^{n} \pm a_i = s $$

After I gave the solution, the interviewer asked: when constructing the table, what should we do if `s` is negative?

<figure class="editorial-illustration">
  <img src="/en/images/ai/sensetime-intern-interview/en-engineering-path.webp" alt="Forest leaves a floating staircase of career trophies and chooses a grounded engineering bench where he can build reliable software by hand" width="1536" height="864" loading="lazy" decoding="async">
</figure>

## Afterword | Reflections

Having a clear career plan and skill direction is very important. When chatting with senior classmates normally, I can say that I do not know what I want to do or what I am most interested in. But saying this in an interview is definitely GG. It is best to have a relatively clear understanding of yourself, know your interests and career plan, and of course it is even better if these match the requirements of the position. If they do not match, the probability of GG is high, and you might even be criticized. See my Face++ interview experience from May, which can be found on my blog: https://youngforest.github.io/en/2018/08/14/face-interview/.

Algorithm positions are extremely hot this year, to the point that supply exceeds demand. Algorithm positions are also upstream in the programmer hierarchy of contempt, and the salary is somewhat higher than development positions.

But combining my own experience and feelings, I found that I am still more interested in writing code: writing more elegant and higher-performance code, satisfying users' needs, and creating impact. That makes me happy and satisfied. Although being a programmer is hard, I am happy. There is no need to chase careers that others consider good. The hierarchy of contempt is roughly: researcher > algorithm engineer > development engineer > others.

In the future, I will also plan my career along the development direction and learn the necessary skills.

I once told a good friend, "I want to write code for a lifetime." It is still true now, and I am even more determined.
