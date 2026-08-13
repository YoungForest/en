---
title: Niz Plum Option Key Does Not Respond in Bluetooth Mode
date: 2021-10-13 12:39:31
tags:
- Keyboard
categories:
- Tinkering
translations:
  zh-CN: https://youngforest.github.io/2021/10/13/Niz-plum-option-key-not-work/
  en: https://youngforest.github.io/en/2021/10/13/Niz-plum-option-key-not-work/
---
Recently, when using my beloved Niz Plum 66 keyboard, I ran into a strange bug.

In Bluetooth mode, the left Option key stopped working. Since my Hammerspoon setup and many other app shortcuts use it heavily, I could occasionally use the right Option key as a temporary substitute, but that was never a real solution. My work efficiency dropped a lot.

I am not sure how this bug was introduced. It was probably around the time my Mac started connecting to two external monitors.

The strange thing is that wired mode had no problem. I also tried disconnecting and reconnecting Bluetooth, but that did not fix it. Switching to another Bluetooth channel did not help either.

I searched a lot online, but could not find a similar issue or solution. In the end, I managed to fix it through my own trial and error, so I am recording it here in case I run into it again in the future.

[Reference manual](http://yunxii.cn/2019/08/12/plum%E9%9D%99%E7%94%B5%E5%AE%B9%E9%94%AE%E7%9B%9866%E9%94%AE%E8%AF%B4%E6%98%8E%E4%B9%A6/).

1. Restore factory settings: hold the four keys at the four corners of the keyboard at the same time for five seconds.
2. Turn on the keyboard and connect it to the Mac.
3. Reconfigure keyboard settings, such as mine below:
    - Fn + Command switches from the default Windows layout back to Mac
    - Fn + ] reduces key latency
    - Fn + Right Shift turns the right Shift into `shift` instead of the default Up
4. The left Option key has been successfully fixed.

Please enjoy your own keyboard!
