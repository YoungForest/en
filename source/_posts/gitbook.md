---
title: Writing a Book with GitBook
date: 2019-07-16 15:34:35
description: "How to organize Markdown notes into a small GitBook project and generate formats for previewing and publishing."
tags:
categories:
- Programming
translations:
  zh-CN: https://youngforest.github.io/2019/07/16/gitbook/
  en: https://youngforest.github.io/en/2019/07/16/gitbook/
---
Unlike a blog, a book is relatively more complete and more systematic. Blogs, by comparison, are much more scattered. However, excellent blog series are often adapted into books.
If you want to share larger-scale, systematic knowledge, writing a small book is a good choice.
This article introduces a tool called `GitBook`, which lets you write a book in Markdown, put it on GitHub, and generate web and PDF versions of the book. Compared with traditional LaTeX, it is simpler and more convenient. It suits contemporary programmers.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/gitbook/en-hero.webp" alt="Loose blank note cards pass through a modular binding workshop and emerge as a structured book, a continuous web-page scroll, and printed folios" width="1536" height="864" decoding="async">
</figure>

<!-- more -->

The references for this article mainly come from the [official website](https://github.com/GitbookIO/gitbook/blob/master/docs/setup.md). By comparison, this article is more focused and can help you quickly initialize, write, and publish a book.

Install gitbook command line tool:
```bash
npm install gitbook-cli -g
```

Create a book:
```bash
gitbook init ./directory
```

Preview and serve your book:
```bash
gitbook serve
```

Or build the static website:
```bash
gitbook build
```

Debug for better error message with stack trace:
```cpp
gitbook build ./ --log=debug --debug
```
