---
title: Logging in to a Server Through an SSH Jump Host
date: 2021-03-26 15:11:02
tags:
- tech
categories:
- Tinkering
translations:
  zh-CN: https://youngforest.github.io/2021/03/26/SSH-proxy-by-jump-server/
  en: https://youngforest.github.io/en/2021/03/26/SSH-proxy-by-jump-server/
---
## Motivation

Following the problem in the previous post, [Fixing Ubuntu Desktop VPN Access to Public Internet Resources](https://youngforest.github.io/en/2021/03/18/Ubuntu-vpn-cannot-access-public-resource/), I tried configuring a jump host to access machines at the Hangzhou Research Institute.

In my research work, my MacBook could not connect to OpenVPN and therefore could not access the Hangzhou Research Institute machines. My Ubuntu desktop already had the VPN configured and could access the servers. I now wanted to route through the Ubuntu desktop so the Mac could "directly" access the Hangzhou Research Institute. Abstracting the problem:

- A can access B
- A cannot access C
- B can access C
- I now want A to access C

Since I mainly use SSH at work, the problem becomes: A logs in to C directly through SSH.
After persistent online searching and experiments, I summarized two techniques and methods that can achieve my goal.

- SSH proxy
- SSH tunnel

<!-- more -->


## SSH Proxy

This method is the simplest. It does not require doing anything on B and connects to C seamlessly.

Run this on A, where `rentao@10.134.150.154` is B, and `ldmc@192.168.131.181` is C.

```bash
ssh -o "ProxyJump rentao@10.134.150.154" ldmc@192.168.131.181
```

Reference: [Traversing a Jump Host](https://www.xiebruce.top/650.html)

## SSH Tunnel

Run this on B:

```bash
ssh -f -N -L 0.0.0.0:9906:192.168.131.181:22 ldmc@192.168.131.181
```

Run this on A:

```bash
ssh -p 9906 ldmc@10.134.150.154
```

Although this method looks more cumbersome and requires coordination between A and B, it is actually more flexible than the first approach. Through an SSH tunnel, B can expose more services from C, not necessarily only SSH login. For example, exposing a TensorBoard HTTP service for observing training data is also something I often need.

Reference: [SSH Port Forwarding: SSH Tunnel](https://www.zsythink.net/archives/2450)
