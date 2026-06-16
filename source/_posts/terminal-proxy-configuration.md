---
title: Implementing a Terminal Proxy
tags: 
  - Linux
categories:
- Tinkering
date: 2016-11-02 23:30:00
---

## Problem
As a Linuxer, being proficient with the terminal is an essential skill. But in the terminal, downloading and installing things can sometimes be very slow, which is frustrating. I naturally wondered whether I could add a proxy to improve the speed. I had been using Shadowsocks before, and the browser used SwitchyProxy, which achieved basic proxy-based internet access. So, is there a similar tool in the terminal? The answer is yes. Through targeted searching, I successfully solved the proxy problem in the terminal. I summarize it here and hope it can help everyone.
<!-- more -->
## Tools Needed
Shadowsocks, Polipo

### Shadowsocks
[Download and Install Client](https://shadowsocks.org/en/download/clients.html)

#### Install Command-line Client
```bash
pip install shadowsocks
apt-get install shadowsocks-libev
cpan Net::Shadowsocks
```
For Shadowsocks configuration, you can refer to [this article](http://bblove.me/2015/03/09/use-ss/). I configured it a long time ago, so I will not recall it here, since I have mostly forgotten it.

Let us start directly from Polipo.

### Polipo
Polipo has multiple installation methods. You can install it directly with Python's package manager `pip`, or use the package manager of each operating system. I recommend the latter because I personally think it is easier to manage. Here I use Ubuntu as an example.

```shell
apt-cache search polipo

adzapper - proxy advertisement zapper add-on
polipo - lightweight, caching web proxy
polipo-dbg - debug package for polipo
```
The second package is the one for beginners like us. Install it:
```shell
sudo apt-get install polipo
```

After installation, it starts automatically by default.
We first need to modify its configuration file, `/etc/polipo/config`:
```
logSyslog = true
logFile = /var/log/polipo/polipo.log
logLevel = 4 # 没有这句话的话`sudo polipo -v`会出现无法查看log文件的情况

socksParentProxy = "localhost:1080" # 这里假设shadowsocks的local port为1080
socksProxyType = socks5 # 是不是和SwitchyProxy的设置很像?
```

For specific configuration options, refer to the file `/usr/share/doc/polipo/examples/config.sample`.

After configuration, we need to restart the Polipo service. Every time the configuration file is modified, the service must be restarted to load it.
```
sudo service polipo stop;
sudo service polipo start;
```
Or directly:
```
sudo service polipo restart;
```

You can use:
```shell
sudo polipo -v
```
to view the specific configuration items, and you can see that our previous changes have taken effect.

## Testing
After configuration is complete, how do you know whether you can already access the internet through the proxy?
You can use the following commands:
```
~$ curl -i http://ip.cn
当前 IP：60.xxx.xxx.x 来自：中国北京北京 xxx/电信
```
```
~$ http_proxy=http://localhost:8123 curl -i http://ip.cn # polipo的默认端口为8123,如有需要可以自行改动
当前 IP：xxx.xxx.xxx.xx0 来自：美国加利福尼亚州洛杉矶
```
This counts as success.

Recently I discovered that the website `ip.cn` no longer works and returns a 500 error. Replacing it with `ipinfo.io/ip` works; this is another website that returns your public IP.
There are also other websites that provide similar IP-returning services:
```bash
curl ifconfig.me
curl icanhazip.com
curl ipecho.net/plain
curl ifconfig.co
```

Ways to obtain your public IP from the browser:
- [Google](https://www.google.com/search?q=what%20is%20my%20IP%20address)
- [DuckDuckGo](https://duckduckgo.com/?q=ip)
- [Wolframalpha](https://www.wolframalpha.com/input/?i=what+is+my+ip+address), recommended

## Going Further
Typing such a long command, `http_proxy=http://localhost:8123`, every time is really not what we want. For convenience, you can type `export http_proxy=http://localhost:8123` in the terminal, meaning it takes effect for all commands in that terminal. Or go one step further and add `export http_proxy=http://localhost:8123` to `.bashrc`, so it runs automatically every time the terminal starts. If you do not want every command to go through the proxy, you can do as I did and add `alias hp="http_proxy=http://localhost:8123"` to `.bashrc`. Then every time you need a proxy, just add `hp ` before the command.

## Configure Proxy for Git
The speed of `git clone` is quite moving, only a few dozen KB/s. Configuring a proxy for Git is also very simple.

Add the following setting to the http items of .gitconfig files.
``` bash
[http]
proxy = <address of the proxy server>:<port of the proxy server>
```

You can also configure it using the following config command:
``` bash
git config --global http.proxy <address of the proxy server>:<port of the proxy server>
```

[Connect to GitHub via SSH](https://gist.github.com/chuyik/02d0d37a49edc162546441092efae6a1)

Enjoy proxy-based internet access in the terminal.

