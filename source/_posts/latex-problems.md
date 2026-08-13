---
title: Problems Encountered While Using LaTeX
date: 2018-03-27 19:27:55
tags:
- TeX
categories:
translations:
  zh-CN: https://youngforest.github.io/2018/03/27/latex-problems/
  en: https://youngforest.github.io/en/2018/03/27/latex-problems/
---
I used LaTeX to write my graduation thesis and encountered many problems. I summarize them here.

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
