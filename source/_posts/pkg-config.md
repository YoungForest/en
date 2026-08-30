---
title: pkg-config
date: 2019-03-17 19:52:38
tags:
- C++
categories:
- Programming
description: "An introduction to how pkg-config exposes library metadata for headers, compiler flags, linking, and public versus private dependencies."
translations:
  zh-CN: https://youngforest.github.io/2019/03/17/pkg-config/
  en: https://youngforest.github.io/en/2019/03/17/pkg-config/
---
Recently I have been learning how to use [google-test](https://github.com/google/googletest) and reading its source code. When I ran `make install`, I noticed that besides installing header files into `/usr/local/` and a shared library into `/usr/lib/`, it also installed two `.pc` files into `/usr/local/lib/pkgconfig/`. So what exactly is `pkg-config`?

From this [guide](https://people.freedesktop.org/~dbn/pkg-config-guide.html), we can find all the useful basic information about `pkg-config`.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/pkg-config/en-hero.webp" alt="A small build cart follows blank catalogue cards through a rail junction to the correct header crates, library vault, and linking gate" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## Overview

Modern computer systems use many layers of components to provide APIs to users. A major challenge is how to integrate these different layers properly. The `pkg-config` tool collects metadata for libraries installed on the system, and users can conveniently inspect that metadata. For example, one of the pc files installed by google-test, `gtest.pc`, contains:

It includes all the information needed to use the gtest library, such as the header installation path, the shared library location, and the compiler options needed at compile time. In other words, to use gtest, reading this metadata is enough.

```pc
prefix=${pcfiledir}/../..
libdir=${prefix}/lib
includedir=${prefix}/include

Name: gtest
Description: GoogleTest (without main() function)
Version: 1.9.0
URL: https://github.com/google/googletest
Libs: -L${libdir} -lgtest 
Cflags: -I${includedir} -DGTEST_HAS_PTHREAD=1 
```

Without a metadata system like `pkg-config` on a computer system, locating and obtaining the details of services provided by the system would be difficult.
For a developer, installing `pkg-config` metadata along with your package can greatly simplify how users consume your API.

The main use of `pkg-config` is to provide the necessary details when a program is compiled and linked against a library. This metadata is stored in `pkg-config` files. These files use the `.pc` suffix and are placed in specific paths known by the `pkg-config` tool.
A `.pc` file contains two kinds of information: metadata keywords and freeform variables.
The former starts with a keyword followed by a colon and value, such as "Name: gtest".
The latter connects a variable name and value with `=`, such as "prefix=...".
Keywords are defined and exported by `pkg-config`.
Variables are optional, but they can be used to represent information not covered by `pkg-config`, or be referenced by keywords to make keyword definitions more flexible.

Ideally, one pc file should correspond to one library file. The filename, except for the suffix, should also be the same.

<figure class="editorial-illustration">
  <img src="/en/images/ai/pkg-config/en-public-private.webp" alt="A library stage exposes only the components used by visitors while private gears and support machinery remain connected backstage" width="1536" height="864" loading="lazy" decoding="async">
</figure>

The most important metadata fields are `Requires`, `Requires.private`, `Cflags`, `Libs`, and `Libs.private`. External projects can use them to compile and link against this library. Prefer using the private fields to avoid exposing unnecessary libraries to users. If the user does not directly use symbols from the required library, they should not link against that library directly.
