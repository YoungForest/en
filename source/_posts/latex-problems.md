---
title: Problems Encountered While Using LaTeX
date: 2018-03-27 19:27:55
tags:
- TeX
categories:
description: "A LaTeX troubleshooting note: a thesis compiles but embedded PDF images stay blank until the output driver is made compatible."
translations:
  zh-CN: https://youngforest.github.io/2018/03/27/latex-problems/
  en: https://youngforest.github.io/en/2018/03/27/latex-problems/
---
I used LaTeX to write my graduation thesis and encountered many problems. I summarize them here.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/latex-problems/en-hero.webp" alt="A finished thesis page passes through a projector with a blank image window until a small compatibility adapter brings the picture into view" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## PDF Cannot Display Correctly
### Problem Description
A document successfully compiled with XeLaTeX could not correctly display PDF images; it showed a blank area instead.

### [Cause](http://tieba.baidu.com/p/4191389769?traceid=)
The PDF file version was the problem. Its version was 1.7, while XeLaTeX compiles as 1.5 by default.

### Solution
For `xelatex %.tex`, add `--output-driver="xdvipdfmx -V7"`. That is, modify `msmake.bat`:
``` bash
xelatex %mythesis%
# =>
xelatex --output-driver="xdvipdfmx -V7" %mythesis%
```
