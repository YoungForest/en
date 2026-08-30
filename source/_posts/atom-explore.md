---
title: A Brief First Taste of Atom
date: 2017-04-08 22:20:36
tags:
- Atom
categories:
- Tinkering
description: "A brief troubleshooting note on installing a Markdown formula-preview package by routing the package manager through a proxy."
translations:
  zh-CN: https://youngforest.github.io/2017/04/08/atom-explore/
  en: https://youngforest.github.io/en/2017/04/08/atom-explore/
---
## Configure Proxy
My requirement was to insert LaTeX formulas into Markdown documents. Atom's default `markdown-preview` package could not satisfy this requirement. After searching, I found that the community package `markdown-preview-plus` could satisfy it.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/atom-explore/en-hero.webp" alt="A formula component parcel reaches a Markdown workshop through a small relay bridge after the direct delivery chute jams" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

### [Atom Package Management](http://flight-manual.atom.io/using-atom/sections/atom-packages/#atom-packages)
Following the official tutorial, the installation failed:
> Installing “markdown-preview-plus@2.4.9” failed.

In the log, I found this request:
 > GET https://atom.io/download/electron/v1.3.13/iojs-v1.3.13.tar.gz

I then tested this URL in the browser. It did not respond, so I guessed it was blocked. Fortunately, the Atom documentation considered the need to use a proxy and provided a guide.

[Configure a proxy for apm](http://flight-manual.atom.io/getting-started/sections/installing-atom/#using-a-proxy)

Finally, basic proxy setup skills. No explanation needed.

### Concrete Commands
``` bash
sean@sean-OptiPlex-790:~$ apm config set https-proxy http://localhost:8123
sean@sean-OptiPlex-790:~$ apm config get https-proxy
http://localhost:8123/
sean@sean-OptiPlex-790:~$ apm install markdown-preview-plus
Installing markdown-preview-plus to /home/sean/.atom/packages ✓
```
