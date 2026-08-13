---
title: Solution for Writing to NTFS on Mac OS
date: 2019-11-11 10:10:44
tags:
- Mac
categories:
- Tech
translations:
  zh-CN: https://youngforest.github.io/2019/11/11/mac-write-ntfs-solution/
  en: https://youngforest.github.io/en/2019/11/11/mac-write-ntfs-solution/
---
Using macOS does have some inconvenient parts, such as writing to NTFS hard drives or USB drives. By default, Mac only supports reading NTFS. But as long as you have the courage to tinker, the solution is still very simple.

## Most Recommended Method

[Mounty for NTFS](https://mounty.app/)

Pros: free and lightweight.
Cons: not hacky. It is basically just a wrapper around command-line operations. Some classmates may prefer the command-line way.

## Most Hacky Method

```bash
sudo umount "/Volumes/Seagate Expansion Drive"
sudo mount -t ntfs -o rw,auto,nobrowse /dev/disk3s1 ~/ntfs-volume
```

Reference: [mounty](https://mounty.app/)

After struggling for a while, I still chose the recommended method of installing a third-party app. The command line is indeed easy to forget or mistype, and every time I have to Google it again. That does not match the elegance and convenience I want from using macOS.
