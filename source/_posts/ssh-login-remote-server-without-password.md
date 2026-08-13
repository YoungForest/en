---
title: SSH Passwordless Login to a Server
date: 2018-05-08 18:47:09
tags:
- Tech
categories:
- Tinkering
translations:
  zh-CN: https://youngforest.github.io/2018/05/08/ssh-login-remote-server-without-password/
  en: https://youngforest.github.io/en/2018/05/08/ssh-login-remote-server-without-password/
---
The following commands are only for Unix-like systems.
Windows does not need this solution. Many PuTTY-like tools on Windows can choose "remember password" to implement passwordless login.
I did not find a similar password-remembering solution on Mac, but using a key is actually a more secure method.

<!--more-->

## Step 1: Generate an RSA Key Pair
``` bash
ssh-keygen -t rsa
```

## Step 2: Upload the RSA Public Key to the Server
``` bash
cat id_rsa.pub | ssh -p 26757 root@138.128.193.150 'cat >> .ssh/authorized_keys'
```

## Step 3: Change Permissions
``` bash
ssh -p 26757 root@138.128.193.150 "chmod 700 .ssh; chmod 640 .ssh/authorized_keys"
```

## Step 4: Success!
```bash
ssh -p 26757 root@138.128.193.150
```

reference: 
[ssh-passwordless-login-using-ssh-keygen-in-5-easy-steps](https://www.tecmint.com/ssh-passwordless-login-using-ssh-keygen-in-5-easy-steps/)
