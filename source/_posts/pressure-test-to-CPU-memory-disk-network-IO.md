---
title: Stress Testing CPU, Memory, Disk Read/Write, and Network
date: 2017-01-14 12:39:23
tags: 
- Benchmark
categories:
- Laboratory
description: A practical record of building adjustable stress tests for CPU, memory, disk I/O, and network, including implementation ideas, code, and usage.
translations:
  zh-CN: https://youngforest.github.io/2017/01/14/pressure-test-to-CPU-memory-disk-network-IO/
  en: https://youngforest.github.io/en/2017/01/14/pressure-test-to-CPU-memory-disk-network-IO/
---
Recently, a senior schoolmate in the lab had a requirement: write four small programs to stress test memory, CPU, disk, and network respectively. The test programs needed adjustable pressure levels, such as high, medium, and low. No more words; roll up the sleeves and get to work. The requirement was simple, but implementation was not. I learned while writing the programs and barely completed the task.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/pressure-test-to-CPU-memory-disk-network-IO/en-hero.webp" alt="One unmarked control lever adjusts the load on a processor wheel, a memory-block tank, a disk rotor, and a two-ended network conduit" width="1536" height="864" decoding="async">
</figure>

<!-- more -->
## Test Environment
Ubuntu 14.04

## CPU
Inspired by the first chapter of *The Beauty of Programming*, "Make the CPU Usage Curve Follow Your Commands," I quickly completed the CPU stress testing program.

#### Basic Idea
Determine a small period. I set the period to 100, and this value can be changed by changing the value of `PIECE`; the period is `100 * PIECE`.

Within each period, `stress` percent of the time is spent in an infinite loop, and the remaining `(100-stress)` percent of the time calls `usleep`, note the difference between `sleep` and `usleep`.

#### Program Source Code

<script src="https://gist.github.com/YoungForest/04080ae9ad932aa1fd7211c05e93b197.js"></script>

#### Usage
``` bash
$ 程序名 stress    # 其中 0<=stress<=100
$ # 例子
$ gcc cpu_benchmark.c -o cpu_benchmark.out
$ cpu_benchmark.out 50
```
## Disk Read/Write
For disk stress testing, I implemented read and write operations with two separate programs.

### Disk Read Operation

#### Basic Idea
Similar to the CPU stress test, within one period, 1s, first read a specific amount of data, `speed`, from disk, then sleep for the remaining time in the period.

#### Program Source Code
<script src="https://gist.github.com/YoungForest/9faaa24df53b3e78d27c18155ee26384.js"></script>

#### Usage
``` bash
$ python3 disk_read.py 硬盘名 level    # 其中level可以是0, 1, 2, 3
$ # 例子
$ python3 disk_read.py /dev/vda 2
```

### Disk Write Operation

#### Basic Idea
The stress test for disk write operations is similar to read operations. Within one period, 1s, write a specific amount of data to a file, then delete it, and sleep for the remaining time in the period.

To write data into the file, I used the command
`dd if=/dev/zero of=/path/to/targetfile bs=1024k count=speed conv=fdatasync > /dev/null 2> /dev/null`.
This command reads data from `/dev/zero`, which is actually invalid data and is often used as a data source for initializing files, and writes it to `/path/to/targetfile`. To avoid the error output and standard output produced by `dd` affecting the readability of this program, I used redirection. `/dev/null` is a black-hole device; any data can be input into it without bad effects.

#### Program Source Code
<script src="https://gist.github.com/YoungForest/0b3fdfbe15821b32991d9d48a9122290.js"></script>

#### Usage
``` bash
$ python3 disk_write.py 硬盘名 level    # 其中level可以是0, 1, 2, 3
$ # 例子
$ python3 disk_write.py /dev/vda 2
```

## Memory

#### Basic Idea

For memory testing, my senior schoolmate introduced me to the program [`memtester`](https://linux.die.net/man/8/memtester). Specific installation and usage:

``` bash
$ # 安装
$ sudo apt-get install memtester
$ # 从PHYSADDR处分配MEMORY大小的内存, 测试ITERATIONS次.
$ sudo memtester [-p PHYSADDR] <MEMORY> [ITERATIONS]
```

In general, there is no need to specify `PHYSADDR`, because it may destroy memory occupied by other processes, which has some risk.

Using the `memtester` program, I did some simple processing and completed the requirement.

#### Program Source Code
<script src="https://gist.github.com/YoungForest/73875763467c9cd1eeccad3496a18398.js"></script>

#### Usage
``` bash
$ python3 memory_benchmark.py <MEMORY> [ITERATIONS]
$ # 例子
$ python3 memory_benchmark.py 3m 10
$ python3 memory_benchmark.py 2m 
```

## Network
The senior schoolmate's requirement was for public network stress testing. I looked through a lot of materials; most tools measured network speed, rather than allowing a specified upload or download rate for testing. Fortunately, after searching for several days, I finally found a tool, `iperf`, that could barely complete the task.

#### Basic Idea
One machine acts as the server, and another machine acts as the client. The client sends packets to the server, the server receives them, and the connection uses UDP. That is to say, if there is no server, the client can still stress test network upload; but without a client, the server cannot stress test network download. In simple terms, the server, download, is passive, while the client, upload, is active. You can still send packets wildly even if nobody receives them, but if nobody sends packets, no matter what, you cannot receive packets.

#### Installation
``` bash
$ sudo apt-get install iperf
```

#### Usage

##### Server
Start the server:
``` bash
$ iperf -s
```

##### Client
``` bash
$ iperf -c 服务器ip -b 压力值
$ # 比如:
$ iperf -c 123.206.61.77 -b 11m
```

#### References
[How to monitor network on Linux](http://www.binarytides.com/linux-commands-monitor-network/)
[Linux network testing and monitoring](https://www.linux.com/learn/five-funny-little-linux-network-testers-and-monitors)
[[Linux] Test network speed in a LAN](http://tuxtweaks.com/2014/11/linux-network-speed-test/?utm_source=tuicool&utm_medium=referral)
