---
title: Django Usage Summary
date: 2017-01-23 19:18:20
description: "A candid look at finishing a database course project in two days with Django, pair programming, documentation, and last-minute teamwork."
tags:
- Database
- Django
categories:
translations:
  zh-CN: https://youngforest.github.io/2017/01/23/Django-note/
  en: https://youngforest.github.io/en/2017/01/23/Django-note/
---
**[Abstract]** [Django](https://www.djangoproject.com/) is a high-level Python web framework that can help us quickly develop and design a clean, programmatic application. Although Django is relatively bulky, since experts nowadays all use [Flask](http://flask.pocoo.org/) maybe, and is more suitable for large web applications, my database course project was a small project. Still, Django's reputation was too strong, and I had previously had a little contact with it in the lab, so my teammate and I decisively chose Django. With Django's help, implementing our course project was as easy as chopping vegetables.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/Django-note/en-hero.webp" alt="Younger Forest and a teammate use a large modular workshop rig to assemble a database foundation, service pipes, and a simple charging-station model" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->
The database course project grades came out recently. I only got Good, and did not get Excellent as I had hoped. But the experience of finishing the database project in two days really taught me a lot: using the Django framework, the concepts of frontend and backend, pair-programming cooperation... What I most want to share is the use of the Django framework. Thanks to this framework, we could focus on backend and database design and implementation, while also easily building a presentable frontend. It achieved twice the result with half the effort and saved us from being killed by procrastination.

<figure class="editorial-illustration">
  <img src="/en/images/ai/Django-note/en-deadline-rescue.webp" alt="Under a nearly empty hourglass, Forest and a teammate repair opposite sides of a course-project machine while loose documentation pages settle into place" width="1536" height="864" loading="lazy" decoding="async">
</figure>

## Time Allocation:

Finishing the database project took two days, and one of those days I was writing code while watching *Lurk*. Before that, I had been learning Django. Most of the documentation was even completed after the defense.

## Personnel Allocation:

The original plan was to lean on `tls`, with all frontend and backend completed by `tls`, while I helped write documentation. Later, however, since `tls` was often conserving energy and kept learning Django, there were still no visible results in the final few hours. I panicked. Based on my previous practice, I patched and improved things, barely implemented the expected functions, and completed the course project. In the end, the documentation was urgently completed by the two of us together, and `tls` helped implement two new features in the last hour.

## Django Usage Summary

The [official documentation](https://docs.djangoproject.com/en/1.10/) is complete and friendly. All needed functionality can be found there. The disadvantage is that it is too large and hard to search; it is also in English. The official documentation updates too quickly, and Chinese translations cannot keep up. I had barely passed CET-6, so this was quite a headache. Still, I prefer reading the official English documentation because it feels more helpful.

### Practice

I followed andrew-liu's tutorial on [building a blog with Django](https://andrew-liu.gitbooks.io/django-blog/content/) to become familiar with step-by-step commands and operations. This tutorial is very suitable for beginners to learn Django's basic commands and file structure. But one obvious bad consequence was that, in the end, our course project, `Electric Vehicle Charging Station Management System`, looked almost like a blog.

## Summary

This post has relatively little practical content, mainly because my skill is still shallow and I do not have more to share. If I need to use Django again next time, I will definitely come back and add more.
