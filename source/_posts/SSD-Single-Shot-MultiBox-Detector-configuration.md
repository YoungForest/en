---
title: 'SSD: Single Shot MultiBox Detector Configuration and Usage Summary'
date: 2017-01-12 23:50:49
tags:
translations:
  zh-CN: https://youngforest.github.io/2017/01/12/SSD-Single-Shot-MultiBox-Detector-configuration/
  en: https://youngforest.github.io/en/2017/01/12/SSD-Single-Shot-MultiBox-Detector-configuration/
---
## Experimental Environment
+ Ubuntu 16.04
+ CPU only, because my own machine had no NVIDIA card. AMD is all tears.

## Environment Configuration
Basically follow the [official documentation](https://github.com/weiliu89/caffe/tree/ssd). Here I only show the parts that differ from the documentation.
<!-- more -->
### Makefile.confile
+ line 8: CPU only
+ line 79, 80: support for python3
+ line 81: /usr/lib/python3.5/dist-packages/numpy/core/include -> /usr/local/lib/python3.5/dist-packages/numpy/core/include
+ line 95: fix fatal of `hdf5`

### Problems Encountered
> ./include/caffe/util/hdf5.hpp:6:18: fatal error: hdf5.h: No such file or directory

Only Ubuntu 15.10+ versions should encounter this problem. The reason is that the paths of the `hdf5` header files and libraries changed in newer versions.
[Solution](https://gist.github.com/wangruohui/679b05fcd1466bb0937f#)

> /usr/bin/ld: cannot find -lopenblas

``` bash
$ locate libopenblas.so
/usr/lib/libopenblas.so.0

$ sudo ln -s libopenblas.so.0 libopenblas.so
```
[Reference](http://stackoverflow.com/questions/335928/ld-cannot-find-an-existing-library)

> /usr/bin/ld: cannot find -lboost_python3

[Solution](https://github.com/BVLC/caffe/issues/4843)
