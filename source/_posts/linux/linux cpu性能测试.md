---
title: linux cpu性能测试
typora-root-url: linux cpu性能测试
tags:
  - linux
  - 性能测试
  - jecson
  - 13700
  - sysbench 
categories:
  - linux
abbrlink: 31cd68fa
date: 2024-03-07 21:52:52
cover:
top_img:
---

## sysbench基础知识

sysbench的cpu测试是在指定时间内，循环进行素数计算

> 素数（也叫质数）就是从1开始的自然数中，无法被整除的数，比如2、3、5、7、11、13、17等。
> 编程公式：对正整数n，如果用2到根号n之间的所有整数去除，均无法整除，则n为素数。

## sysbench安装

```
# CentOS7下可使用yum安装
yum install sysbench

#ubuntu安装
sudo apt install sysbench
```

## CPU压测命令

```
#素数上限2万，默认10秒
sysbench cpu --cpu-max-prime=20000 --threads=8 run
sysbench cpu --cpu-max-prime=20000 --threads=1 run  #默认是单线程
```

## 常用参数

**--cpu-max-prime**: 素数生成数量的上限

```text
- 若设置为3，则表示2、3、5（这样要计算1-5共5次）
- 若设置为10，则表示2、3、5、7、11、13、17、19、23、29（这样要计算1-29共29次）
- 默认值为10000
```

**--threads**: 线程数

```text
- 若设置为1，则sysbench仅启动1个线程进行素数的计算
- 若设置为2，则sysbench会启动2个线程，同时分别进行素数的计算
- 默认值为1
```

**--time**: 运行时长，单位秒

```text
- 若设置为5，则sysbench会在5秒内循环往复进行素数计算，
  从输出结果可以看到在5秒内完成了几次，
  比如配合--cpu-max-prime=3，则表示第一轮算得3个素数，
  如果时间还有剩就再进行一轮素数计算，直到时间耗尽。
  每完成一轮就叫一个event
- 默认值为10
- 相同时间，比较的是谁完成的event多
```

**--events**: event上限次数

```text
- 若设置为100，则表示当完成100次event后，即使时间还有剩，也停止运行
- 默认值为0，则表示不限event次数
- 相同event次数，比较的是谁用时更少
```

## 结果

##### 8核心 13700  ubuntu虚拟机

多核110598

单核13824

```
110598-8核心

sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 8 //运行线程数8
Initializing random number generator from current time

Prime numbers limit: 20000  // 每个线程产生的素数上限均为20000个

Initializing worker threads...

Threads started!

CPU speed:
    events per second: 11058.04 // 所有线程每秒完成了11058次event

General statistics:
    total time:                          10.0007s
    total number of events:              110598 // 10秒内所有线程一共完成了110598次event

Latency (ms):
         min:                                    0.60
         avg:                                    0.72
         max:                                    6.90
         95th percentile:                        0.81
         sum:                                79972.90

Threads fairness:
    events (avg/stddev):           13824.7500/89.70// 平均每个线程完成13824次event，标准差为89.70
    execution time (avg/stddev):   9.9966/0.00 // 每个线程平均耗时10秒，标准差为0
```

##### jecson 4核心arm

4核心18395

单核 2299

可以看出13700 的8个大核心是jecson的性能的5-10倍

## 参考

[linux sysbench: CPU性能测试详解 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/56889337)