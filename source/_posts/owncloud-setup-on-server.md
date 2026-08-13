---
title: owncloud setup on server
date: 2017-08-20 22:42:32
tags:
  - Ubuntu
  - Owncloud
categories:
translations:
  zh-CN: https://youngforest.github.io/2017/08/20/owncloud-setup-on-server/
  en: https://youngforest.github.io/en/2017/08/20/owncloud-setup-on-server/
---
## Introduction
[ownCloud](https://owncloud.org/) is a private cloud solution that can replace Baidu Cloud. It provides both enterprise and personal editions, and individuals can use a server to build the personal edition of ownCloud. The official website provides multiple server-side solutions. Compiling it yourself can get the latest version of ownCloud; ownCloud also provides [binary installation packages](https://download.owncloud.org/download/repositories/stable/owncloud/) for major distribution repositories, which are more convenient and faster, and more suitable for personal use.

<!-- more -->
## Server Environment
A Tencent Cloud virtual machine. The operating system version is Ubuntu 14.04.5 LTS (GNU/Linux 3.13.0-105-generic x86_64).

## Setup Process
### Install ownCloud
[Install ownCloud through the apt repository](https://download.owncloud.org/download/repositories/stable/owncloud/). Many dependency packages will also be installed, such as `apache2`, the PHP environment, and so on.

### Start the Apache Service
```
service httpd start
```

### Manage and Configure ownCloud
Enter `ip/owncloud` or `domain/owncloud` in the browser address bar, then set the administrator account and password.

#### Configure MySQL
[Reference](http://www.cnblogs.com/mr-wid/archive/2013/05/09/3068229.html)

```
mysql --user=root --password
```

## Client install
### windows
### Android
### Linux
