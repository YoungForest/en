---
title: "From Beijing to Dublin: A Programmer's Journey to Europe"
description: "A practical account of finding a European software engineering job from China, from preparation to relocation."
date: 2022-12-09 16:42:27
tags:
  - life
  - Tech
categories:
  - Diary
translations:
  zh-CN: https://youngforest.github.io/2022/12/09/Find-a-Europe-SDE-job-from-China/
  en: https://youngforest.github.io/en/2022/12/09/Find-a-Europe-SDE-job-from-China/
---
Reposted from [my blog](https://youngforest.github.io/en/2022/12/09/Find-a-Europe-SDE-job-from-China/)

This year, according to plan, I applied from China to programmer positions in Europe. Although I happened to encounter a macroeconomic downturn, with companies freezing hiring or even laying off people one after another, I was lucky enough to still receive two offers and successfully onboard in November, completing the physical move. Although there were many bumps along the way, looking back, everything can be considered smooth. On the road of "running," I received help from many experienced group friends. Therefore I wrote this blog post to summarize my experience and lessons, for the convenience of those who come later.

![Ran away](/images/润了.png)

## Background

First, for friends who are not familiar with me, I will briefly introduce my background.

I studied computer science for both bachelor's and master's degrees at a university in Beijing.

During school, starting from senior year, I had quite a few internship experiences, successively at small companies, big companies, artificial intelligence, autonomous driving, domestic internet companies, and foreign companies. I also had long-term research experience at school.

Internships are a good low-cost trial-and-error opportunity.

After these attempts, combined with my actual situation, I confirmed my goals: look for a job rather than do research; go to a big company rather than a small company; go to a foreign company rather than a domestic company.

At the beginning of 2020, because of my ex, I confirmed the plan to move to Europe for work. Because of the 10043 restriction, she could not go to the US to study. She was also not interested in continuing study in China, so at that time she decided to choose a European university for graduate study, planning to come in autumn 2022 for a master's degree.

After graduating with my master's degree in 2021, I got my wish and worked at a well-known international foreign company in Beijing for one year and four months. This experience also greatly helped me get many interviews on my resume.

Singapore, Japan, Europe, Canada, and Australia are all popular destinations for programmers moving abroad.

Compared with the United States, work-permit systems in these regions are very friendly. Basically, as long as you get an offer, it is easy to get a work permit.

At the same time, many developed countries have welfare systems from cradle to grave, especially suitable for peers escaping domestic 996 and bringing family.

Most importantly, there are many job opportunities, low competitive pressure, and many positions recruiting from overseas. From my experience, if we do not consider language challenges, interview difficulty is far lower than in China.

Although this article is based on applying to Europe, most experience is also helpful for other regions, especially when applying to American companies such as Google, Amazon, and Microsoft.

## Timeline

In March, I began applying on Stack Overflow (the Jobs section is now closed), Google Jobs, and LinkedIn, and also asked many friends for referrals. My target work locations were mainly in the Netherlands, but I also applied to Germany, Ireland, the UK, and other countries.

During this period, I focused on small and medium-sized companies, wanting to practice before applying to big companies. But it was not ideal. Most directly rejected me, and only four gave interviews.

At the end of April, I began applying to big companies. Of course, mass applications never stopped. In the end I applied to nearly 40 companies, got interviews from 8, and received 2 offers. The local big-company positions in Amsterdam were only Amazon, Uber, and Booking. I applied to Microsoft in Dublin and Google in Munich.

Because I was already working at Amazon, I could not apply externally. Internal applications also had restrictions for cross-country positions requiring L5 (SDE 2) plus one year, which I did not satisfy. Although I internally talked with several managers, none could give an exception. Therefore I missed Amazon's European opportunities. When Uber was scheduling a Phone Interview, it entered hire freeze and the other side directly could not find people. Booking simply did not give me an interview.

In June I interviewed with Microsoft and Google.

Microsoft was one Online Assessment (3 algorithm problems, LeetCode Medium difficulty) + 3 rounds of Virtual Onsite (Algorithm + Behavior Questions, Software Design + Behavior Questions, System Design + Behavior Questions) + 1 round of Virtual Onsite (Behavior Questions). It can be seen that Microsoft has a large proportion of Behavior Questions, appearing in every round. Software Design is essentially OOD (Object Oriented Design). Because I misunderstood it and did not prepare correctly, my second round went rather badly. But in the end I still got the offer.

Google was 1 round Phone Interview (Algorithm) + 4 rounds Virtual Onsite (3 rounds Algorithm + 1 round Behavior Questions). It can be seen that Google's algorithm proportion is very high. Below Senior, there is no system design interview. Because I had practiced a lot of LeetCode, Google's interview style was very enjoyable for me, and I felt the result was good. After that came the long Team Match. In the end, because of HC Freeze, the process ended. Google's process is very long. The recommended approach is to apply to Google first, and after Google's interviews finish, apply to other big companies. This can make offer timing roughly align and make it convenient to compete.

In July, I received two offers on the same day and negotiated compensation.

In August I accepted the offer. In September I handled the visa. In October I resigned. In November I onboarded.

As you can see, from starting to apply to big companies to onboarding, basically half a year passed. Everyone can prepare according to their own situation.

In addition, I spent quite a lot of time on Pramp mock interviews and interviews with small and medium-sized companies. The main reason was that I lacked confidence early on and wanted to practice before rushing big companies. Looking back, it was not very useful.

First, small-company and big-company interview modes and content differ greatly, so the practice effect is average.

Second, because I had only one year of work experience, I received very few interviews from small companies. After all, relocating someone from overseas takes longer to onboard and is not cheap. They prefer hiring Senior people.

Finally, because I encountered this year's harsh macro environment, applying to big companies a few months later was deadly. By June and July, news of Hire Freeze or Layoff arrived one after another. Big companies do value ability, but luck is also very important. If I had been later, it would have been even worse. The Microsoft team whose offer I finally accepted was not the team I interviewed with at first. The first team had Hire Freeze. Fortunately, the team I joined was very foundational and important, and even against the background of company-wide Hire Freeze, it still managed to secure a small amount of Head Count.

## Interview Preparation

This part is what everyone cares about most and also spends the most time on.

First, I recommend the [V2WORLD group owner's channel](https://www.youtube.com/@Fred_Talk). It has very practical videos covering resume preparation, English, algorithms, skills, and more, especially suitable for German positions.

Here I mainly combine my own experience and share some thoughts on interview preparation.

### English

English may be a challenge many students fear and worry about. I was the same, so I understand very well.

As a small-town exam taker, Gaokao English listening did not even count toward the score. From childhood to adulthood, my English teachers were also graduates from second-tier or even junior colleges, and their own spoken English was not fluent. CET-4 and CET-6 did have listening, but spoken English was optional at the time, and not many people specially took it. University English teachers were quite strong, but unfortunately the foundation was already there. Also, university English focused more on research needs. Freshman year was the most terrifying time: English classes were changed to full English, and I could not understand lectures. Some classmates handled it easily, corresponding to the obvious gaps between urban and rural areas and between developed and underdeveloped regions. Starting from university, I began a long struggle with English. Although I passed CET-4 and CET-6 on the first try without preparation, I deeply knew that this only represented test-taking ability and my strength in reading. Listening and speaking were still much worse than classmates from big cities. I tried many English apps at the time, including Liulishuo and Shanbay. Shanbay's website was well made, and as a programmer, I was more used to PC products. Also, Shanbay Listening's intensive-listening function was exactly what I liked. My roommate and I checked in daily for nearly two years.

In addition, I also tried participating in some English activities at school, including trying the English club a few times and loudly reciting by Shahe South Lake early every morning. As a volunteer, I participated in two summer schools and solved various problems for foreign students. I also attended a summer-school course as a student.

Using exams to promote learning is also a good path. At that time there was no 10043 Act yet, and the proportion of Beihang students going abroad was quite high, probably around 20% in the end. Adding those who had some study-abroad ideas, perhaps 50%. I was influenced and took TOEFL. Although I only scored 91 in the end, preparing for it indeed improved my English a lot, especially listening.

Later, going to Belgium on exchange also gave me opportunities to practice English and communication.

Although so far I still find a considerable gap between my English and the people around me, it basically does not affect work much. During interviews, because people in Europe are basically not native English speakers either, the requirements and level are relatively lower. Even so, there were still interviewers who rejected me because they currently wanted someone with better English.

In my view, English in interviews is not difficult. The most important thing is that you dare to speak and are willing to communicate. If you encounter something you do not know how to express, or you have not expressed clearly, as long as you repeatedly communicate and confirm with the interviewer, there is basically no communication barrier. Work is actually the same.

Therefore, my advice is: use mock interviews such as [pramp](https://www.pramp.com/invt/lYgBVdQ8qrTMQg9jwEmP), or real interviews by first applying to some small companies, to practice and find weaknesses and techniques. Whether English or technical ability, European interviews are really not as difficult as most people imagine.

### Resume Submission

Mainly rely on referrals from online friends and LinkedIn mass applications. Spend some time maintaining a good LinkedIn profile. Basically use referrals for big companies and mass applications for others.

### Algorithm Problems

You still need to grind LeetCode. About 300 problems is basically enough.

My previous experience is shared here: [How does everyone grind LeetCode? - youngforest's answer on Zhihu](https://www.zhihu.com/question/280279208/answer/704774024)

### Software Design, OOD

Basically this one book is enough:

- *Grokking the Object Oriented Design Interview*

Other advanced materials:

- [The System Design Primer](https://github.com/donnemartin/system-design-primer)

### System Design

For interview preparation, basically two books are enough:

- *Grokking the System Design Interview*
- *Alex Xu - System Design Interview: An Insider's Guide*

If you do not have time, prioritize the first one.

Besides handling interviews, system design is actually very important for work too. I also recommend some resources here to improve your actual system-design ability, rather than only dealing with interviews.

- DDIA (*Designing Data-Intensive Applications*)
- [A distributed-systems ebook and video-resource collection](https://www.1point3acres.com/bbs/thread-907316-1-1.html)

### Behavior Interview

Behavior questions are easy to prepare. One or two weeks is enough. There are many questions online. Prepare several stories in advance based on your past experience, enough to handle most questions. They must be based on your actual past experience. You can embellish, but do not fabricate out of nothing, otherwise if the interviewer asks follow-ups, you may not be able to keep the story together and will be exposed.

Articles:

- [1Point3Acres](https://www.1point3acres.com/bbs/google_search.php?q=behavior%20question)
- [1Point3Acres Behavior questions collection](https://www.1point3acres.com/bbs/thread-529217-1-1.html)

Podcasts:

- [softskills](https://softskills.audio/)
- [Soft Skills Engineering](https://podcasts.google.com/feed/aHR0cHM6Ly9zb2Z0c2tpbGxzLmF1ZGlvL2ZlZWQueG1s?sa=X&ved=0CAMQ4aUDahgKEwiA2Mv__tL3AhUAAAAAHQAAAAAQ4xo)

YouTube:

- [DanCroitor](https://www.youtube.com/watch?v=IyPJFHFUpbQ&ab_channel=DanCroitor)

## How Is Ireland?

In the past two years, I also joined a WeChat group of like-minded people moving abroad (V2WORLD), and met many group friends who have moved, are moving, or plan to move. Honestly, in 2022, because of China's economy and pandemic lockdowns, more people wanted to move.

From this small sample (about 200 people, likely with survivorship bias), most moved to Singapore or Europe, with Germany accounting for most of the European cases. Japan, Canada, and Australia came next.

Ireland is not a particularly popular or eye-catching country. In fact, when I applied, I did not mainly target Ireland. But later I discovered there are many big companies here, including many American tech giants' European headquarters, such as Microsoft, Apple, Google, and AWS (Amazon's European headquarters is in Luxembourg). Basically they all have offices with around 3,000 R&D staff. For example, Microsoft here has two buildings and 3,500+ R&D staff. There are many job opportunities, especially if you prefer American big tech companies or want to move to the US later through L1. Job hopping and career development later are also good, and it is called Europe's little Silicon Valley.

Besides programmers having relatively good job prospects, pharmaceutical companies are also a pillar industry in Ireland.

In addition, Ireland's immigration policy is very friendly. With an offer, you can apply for a work permit and then a work visa. For programmers, it is basically a Critical Skills Work Permit. In the first year you cannot change jobs. In the first two years (Stamp 1), you need employer sponsorship for the Work Permit. After two years (Stamp 4), you can freely change jobs and be self-employed. Spouse visas can also study and work. [The fastest path to a passport is five years, though Ireland also has physical-presence requirements: time outside Ireland each year must not exceed six weeks, otherwise that year does not count toward your five years.](https://www.citizensinformation.ie/en/moving_country/irish_citizenship/becoming_an_irish_citizen_through_naturalisation.html#l9d317) Unlike other countries, there are no language requirements or integration exams. I recommend my Microsoft colleague's posts: [Explanation of Ireland's immigration visa system](https://www.1point3acres.com/bbs/thread-901327-1-1.html) and [official explanations of various residence stamps](https://www.irishimmigration.ie/registering-your-immigration-permission/information-on-registering/immigration-permission-stamps/#5b7196a7b9ecd4ab3). I feel Ireland is actually a value depression: many jobs and little competition. After chatting with quite a few people, I feel interviews are simpler too.

For many people moving abroad, the main purpose is to escape the grind of domestic 996 and seek a better life. Ireland, like other countries, can satisfy this need. It also has a relatively complete welfare system, fully enough to live the life of "wife, children, and a warm kang."

Ireland also has many shortcomings.

Dublin's infrastructure is extremely poor. It is worse than the capitals of most developed European countries, even worse than Greece. There is no metro, only two light-rail lines, and public transportation is mainly buses. To go in different directions, you need to transfer through the city center. The airport has no light rail or train connection, only coaches and buses. Airport facilities, such as security, are also quite primitive and slow. My analysis of why Ireland's infrastructure is relatively poor: 1. Low population density. Ireland has only 4 million people, far fewer than China and other European regions, so infrastructure cost-effectiveness is low and cannot keep up. 2. Weak foundation. Historically, it was long a British colony, faced long-term exploitation, and mainly developed agriculture. Its economic takeoff into an industrial country happened only after World War II. Unlike other European countries, it does not have hundreds of years of industrial history and construction.

The climate cannot be called harsh, but it is definitely not very pleasant. In this respect, Northwestern Europe is largely similar: a temperate oceanic climate. Temperature is okay: summer is not very hot, below 30 degrees, and winter is not very cold, above -10 degrees. But each year has half a year of rainy season, concentrated in winter. Wind plus rain is truly physical damage plus magic damage. Because the latitude is high, winter daylight is short, and with rainy-season cloudy days, the sun is rarely seen, easily making people depressed. I recommend supplementing vitamin D through supplements or food. I grew up in northern China until now, and really cannot handle it. However, according to friends from the south (Hangzhou), the climate here is actually better than southern China.

It is rather village-like, also called "Irish village." Besides the poor infrastructure mentioned earlier, malls and entertainment also do not look like those of a big city. But this is fine for me; I actually like it. As a typical "small-town exam taker," I grew up in a small county. Although I later entered a big city through Gaokao, I spent the first two years in Shahe, a town in Beijing's Changping suburbs, which was very desolate. Later I moved to Xueyuan Road near the North Fourth Ring, finally a city. But Haidian was still much older and more broken-down than Chaoyang. After working, I stayed in Chaoyang for one year, finally tasting some big-city glitter, but I was not used to it. So coming to the Dublin suburbs in Ireland this time is acceptable to me. It feels much better than the county where I grew up.

High taxes. Ireland is a high-tax, high-welfare country, valuing fairness more than efficiency. Salaries are already much lower than in the US, and with various taxes up to 52%, take-home pay is actually similar to China or even less. Add high rent and high prices, and the money saved is inevitably less.

Abnormal rent. I previously shared in my [year-end review](https://youngforest.github.io/en/2022/12/25/2022-summary-and-2023-resolutions/#%E7%94%9F%E6%B4%BB) that Dublin's rent is catching up with Europe's first-tier cities, while the infrastructure and services enjoyed are third-tier.

## FAQ

Friends who want to move abroad often ask me some questions, so I organize them here.

**Q: When you interviewed for overseas positions while employed, was the time too tight to prepare at all? Was it hard to take leave?**

A: I was okay. Because I was already working at a foreign company in China, work pressure was low, taking leave was very convenient, and I was also long-term work from home. In addition, for European interviews, I prioritized evening interviews. The worst case was afternoon interviews, which required leave. As for preparation, because I had long practiced LeetCode, I basically did not need much extra time there. I did spend quite a lot of time practicing English, system design, and Behavior questions. If your current work pressure is high, it will indeed be hard to withstand. I suggest taking it slowly. If you truly want to move abroad, do not give up the goal; prepare long term. The macro environment is also bad now, with layoffs and hiring cuts everywhere. Also, using interviews as practice and casting a wide net is important. You do not need to wait until you are fully prepared before applying.

**Q: I want to go to the United States. Why do people rarely share experience applying directly to the US from overseas?**

A: Because the United States is the center of computer technology, and salary and opportunities are also abundant. Many young students may yearn for it.

However, because the US work visa H1B is a lottery system and extremely hard to win, applying directly from overseas is now almost impossible.

The mainstream methods are: 1. Study for a master's degree in the US, then work after graduation using an OPT visa (29 months). 2. Go to an overseas office of an American company, then after one year transfer to the US headquarters with an L1 visa (5 years).

But after going there, you still need to draw H1B to change jobs.

Green-card queues are also based on place of birth. Mainland China and India are hell-level; now it is already six years or more. You must know that when working overseas, status is an important factor determining happiness.
