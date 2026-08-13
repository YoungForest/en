---
title: Bypassing Baidu Cloud Download Speed Limits
date: 2018-11-05 16:44:00
tags:
- Tech
categories:
translations:
  zh-CN: https://youngforest.github.io/2018/11/05/baidu-cloud-download-speed-hack/
  en: https://youngforest.github.io/en/2018/11/05/baidu-cloud-download-speed-hack/
---
Recently, because of English study needs, I often download large files from Baidu Cloud. As everyone knows, Baidu Cloud limits download speed. Without their membership, the download speed is only a few dozen KB/s. I really could not tolerate it, so I searched for speed-limit bypass tools and got the download speed up to 15 MB/s, haha. Sharing it here with everyone.
One thing to note: Baidu Cloud also updates its speed-limit mechanism to prevent abuse, so it is not surprising if the method in this article stops working. You can still search online for other updated methods. Trust the power of programmers.
As of November 5, 2018, this method is workable.

<!-- more -->

[Get Baidu Cloud raw links](https://github.com/gxvv/ex-baiduyunpan)

After running the script, Baidu Cloud's download interface will look like this.

![Ex-Baidu Cloud script result](/images/ex-baiduyun.jpg)

Why click the compressed-link option? Because I found that using "copy link" does not obtain the download address of the file itself; downloading the compressed package is also faster.

[HTTP multi-threaded download tool](https://github.com/proxyee-down-org/proxyee-down)

![Download tool result](/images/proxyee-down.png)
