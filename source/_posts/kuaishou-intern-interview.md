---
title: Kuaishou Intern Interview
date: 2018-04-30 23:33:10
tags:
- Kuaishou
- Internship
categories:
- Interview
description: A review of two algorithm-intern interviews, preparation gaps, onboarding, and a practical study path centered on data structures and algorithms.
translations:
  zh-CN: https://youngforest.github.io/2018/04/30/kuaishou-intern-interview/
  en: https://youngforest.github.io/en/2018/04/30/kuaishou-intern-interview/
---
Thanks to senior schoolmate @lxc for the referral and interview advice, I was fortunate enough to attend the interview for an algorithm intern role in Kuaishou's recommendation group, and I eventually passed the interview and received the offer.
Although intern interviews may be relatively simple, I think this still has reference value.
Doing internships can be considered one route toward eventually finding a good job and joining a dream company. It cannot really be called a shortcut; the two internships I did were both quite tiring. Kuaishou was my second internship.
Now that the internship has finally stabilized, and there is still some time before the deadline for my graduation thesis, I happen to have time to summarize my interview and internship.
I hope this can guide my future search for other internships or full-time jobs :)

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/kuaishou-intern-interview/en-hero.webp" alt="Forest crosses two unmarked interview gates along a route of array tracks, memory boxes, and tree branches toward a bright internship workbench" width="1536" height="864" decoding="async">
</figure>

<!-- more -->

## Interview

I went through two rounds of interviews in total, taking nearly two hours.
Overall, I felt that my preparation was not sufficient, and many areas still needed improvement.

### First Round

The interviewer was a friendly, slightly chubby engineer. He was very kind and took the interview seriously.
After I started the internship, I often ran into him during meetings and meals, which felt very familiar. We were not in the same team, but we were in the same larger group.

Interview content:

- Given an array, find the contiguous subsequence with the maximum sum and return the sum;
- Reverse a sentence: for example, today is wednesday! => wednesday is today!
- SQL-related: differences between left join, right join, and inner join; whether I knew partition;
- Machine learning-related: definitions of precision and recall.

### Second Round

The interviewer was a serious engineer. My impression was that he reacted very quickly, possibly someone at the leader level.
Unfortunately, after joining I never saw him in the larger group.

- When memory is extremely small, find the position of number `m` among a large number of values.
- When `a = b`, where might `b` be obtained from? Register, cache, memory, external storage.
- Given a sorted array cyclically shifted left by an unknown `k`, find the position of number `m`.
- There are `n` stairs, and you can walk 1 step or 2 steps... He first asked whether I had done this problem before. I said yes, so he did not ask me to do it again.
- Given a binary tree where each node stores a number, find the lowest common ancestor of two numbers.
- Differences between multi-process and multi-threading;
- Use of inter-process communication in Java and C++;
- Use of socket libraries for network communication in Java and C++. (I did not answer these two well.)
- My level of understanding of machine learning, and whether I had studied loss functions.

<figure class="editorial-illustration">
  <img src="/en/images/ai/kuaishou-intern-interview/en-preparation-path.webp" alt="Forest reorganizes scattered problem pieces into an interview-preparation road with a strong central route and supporting side paths" width="1536" height="864" loading="lazy" decoding="async">
</figure>

### Interview Preparation

After this interview, I became clearer about what to prepare for interviews:

- Data structures and algorithms are the foundation, and must be reviewed and practiced carefully.
- SQL, machine learning, and Hadoop are bonus skills.
- Handwritten code and short-answer questions are the main content of interviews.

In fact, I answered the SQL, machine learning, and multi-threading questions very poorly. Only the algorithm problems were answered passably; I did write them all out, although there were some mistakes in the middle and they may have taken a long time, especially the first handwritten problem, where I had not yet entered the right state and spent extra time.

Based on my senior schoolmate's advice, I think the following preparation path and resources are relatively efficient, and I am still preparing:

- Data structures and algorithms: COS226 + *Algorithms, 4th Edition*, *Elements of Programming Interviews*
- SQL: *Sams Teach Yourself SQL in 10 Minutes*, [runoob](http://www.runoob.com/sql/sql-constraints.html)
- Multi-threading: *Java Concurrency in Practice*
- Machine learning: Zhou Zhihua's *Machine Learning*, also known as the "Watermelon Book"

## Onboarding

Earlier, because of my graduation thesis, I hesitated over whether to accept the internship offer.<!--and keeping my grandma company-->
After consulting my senior schoolmate and weighing the pros and cons, I finally accepted the offer and joined on April 19.<!--persuaded by my grandma-->
The offer package was: 300 per day, lunch, afternoon tea, and dinner covered, with fruit, milk, and ice cream available freely.
In reality:
300 per day before tax. Interns are taxed as service income, and the portion above 800 is taxed by the state at 20%.
Lunch and dinner are still great, especially the lunch buffet; I overeat every time.
Afternoon tea is simple: a kraft paper bag with drinks, fruit, and snacks. It differs from what I imagined, but is still quite satisfying; I often leave some and take it back to eat.

From the little details at work, I can feel that
Kuaishou is a company with strong technology and many excellent people. It is now expanding rapidly; almost every week someone joins our group (is that why I was admitted?).
The two mentors guiding me are also very nice. I have learned and grown a lot from work.

<!--
Work content includes:
- using pyplot for data visualization
- using hive sql for data preprocessing
-->
