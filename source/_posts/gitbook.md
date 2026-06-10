---
title: Writing a Book with GitBook
date: 2019-07-16 15:34:35
tags:
categories:
- Programming
---

Unlike a blog, a book is relatively more complete and more systematic. Blogs, by comparison, are much more scattered. However, excellent blog series are often adapted into books.
If you want to share larger-scale, systematic knowledge, writing a small book is a good choice.
This article introduces a tool called `GitBook`, which lets you write a book in Markdown, put it on GitHub, and generate web and PDF versions of the book. Compared with traditional LaTeX, it is simpler and more convenient. It suits contemporary programmers.

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
