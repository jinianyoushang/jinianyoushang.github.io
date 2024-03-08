---
title: linux cpu性能测试
typora-root-url: linux cpu性能测试
tags:
  - linux
  - 性能测试
  - jecson
  - 13700
categories:
  - linux
abbrlink: 31cd68fa
date: 2024-03-07 21:52:52
cover:
top_img:
---

## CPU压测命令

```
sysbench cpu --cpu-max-prime=20000 --threads=8 run
sysbench cpu --cpu-max-prime=20000 --threads=1 run  #默认是单线程
```

##### 8核心 13700  ubuntu虚拟机

多核110598

单核13824

```
110598-8核心

sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 8
Initializing random number generator from current time


Prime numbers limit: 20000

Initializing worker threads...

Threads started!

CPU speed:
    events per second: 11058.04

General statistics:
    total time:                          10.0007s
    total number of events:              110598

Latency (ms):
         min:                                    0.60
         avg:                                    0.72
         max:                                    6.90
         95th percentile:                        0.81
         sum:                                79972.90

Threads fairness:
    events (avg/stddev):           13824.7500/89.70
    execution time (avg/stddev):   9.9966/0.00
```

##### jecson 4核心arm

4核心18395

单核 2299

可以看出13700 的8个大核心是jecson的性能的5-10倍

## 参考

[linux sysbench: CPU性能测试详解 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/56889337)