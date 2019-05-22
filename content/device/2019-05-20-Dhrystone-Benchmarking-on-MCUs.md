---
categories:
- Device Tips
date: "2019-05-20"
layout: post
page_source: ControllingMosfetsUsingMCUPins
tags:
- microcontroller
- programming
- cortex-m
title: Dhrystone Benchmarking on MCUs
---

![Getting the cache to work](/images/working-on-cache-execution.png)
The [dhrystone](https://github.com/StratifyLabs/sostest/tree/master/dhrystone) benchmark application for Stratify OS is up and running. Dhrystone is a simple benchmark application that is designed to mimic a typical program. It characterizes the performance of both the compiler and the processor (and in this case the operating system). The result of the program is a number called DMIPS which is the amount of time to run one "dhrystone" divided by 1757 which is the number of dhrystones some old 1 MIPS processor could output.

I ran the benchmark on a variety of processors. The benchmark was running inside of Stratify OS. So there are a couple of factors that reduce the maximum possible DMIPS score that is advertised by chip manufacturers.

- The application was compiled using mlong-calls which has a 5% to 10% penalty. 
- The kernel (including libc) was compiled with -Os rather than being purely speed optimized.
- In some cases, I may have just made an error on the cache/flash settings that is causing degraded performance. I will update this post if I find any issues.
- The application is running with the OS. Some processor time is being used by context switching and providing metrics over USB. This takes away another 10% or so as shown by the following task analysis from `sl app.run:path=dhrystone,terminal task.analyze`. The output was taken from a run with the STM32H743 but is rougly applicable to all tests.

```
summary: 
dhrystone-1.2: 
info: 
    name: dhrystone
    id: 2
    pid: 1
    memorySize: 32768
    stack: 0x2407FCF0
    heap: 0x24078000
activity: 
    cpuTime: 492453363
    cpuUsage: 92.787010%
    maximumStack: 808
    maximumHeap: 12696
    maximumMemoryUsage: 41.21%
```

## The Results

Processor | CPU | DMIPS | DMIPS/MHz | Notes
----------|-----|-------|------|-----------
RT1052 | 525MHz | 675 DMIPS | 1.29 DMIPS/MHz | Running from external SDRAM with **cache on**
RT1052 | 525MHz | 26 DMIPS |  0.05 DMIPS/MHz | Running from external SDRAM with **cache off**
STM32H743ZI | 400MHz | 457 DMIPS |  1.14 DMIPS/MHz | Running from internal flash/RAM with **cache on**
STM32H743ZI | 400MHz | 79 DMIPS |  0.20 DMIPS/MHz | Running from internal flash/RAM with **cache off**
STM32F746ZG | 216MHz | 231 DMIPS |  1.07 DMIPS/MHz | Running from internal flash/RAM with **cache and flash accelerator on**
STM32F746ZG | 216MHz | 120 DMIPS |  0.55 DMIPS/MHz | Running from internal flash/RAM with **cache off and flash accelerator on**
STM32F746ZG | 216MHz | 53 DMIPS |  0.25 DMIPS/MHz | Running from internal flash/RAM with **cache and flash accelerator off**
STM32F723ZG | 216MHz | 246 DMIPS |  1.14 DMIPS/MHz | Running from internal flash/RAM with **cache on and flash accelerator off**
STM32F723ZG | 216MHz | 53 DMIPS |  0.25 DMIPS/MHz | Running from internal flash/RAM with **cache off and flash accelerator off**
STM32F446ZE | 168MHz | 106 DMIPS |  0.63 DMIPS/MHz | Running from internal flash with **data/instruction cache on**
STM32F446ZE | 168MHz | 52 DMIPS |  0.31 DMIPS/MHz | Running from internal flash/RAM with **data/instruction cache off**
LPC4078 | 120MHz | 85 DMIPS |  0.71 DMIPS/MHz | Running from **internal ram**
LPC4078 | 120MHz | 63 DMIPS |  0.53 DMIPS/MHz | Running from **internal flash**
LPC1768 | 96MHz | 51 DMIPS |  0.53 DMIPS/MHz | Running from internal flash
STM32L475VG | 80MHz | 52 DMIPS |  0.65 DMIPS/MHz | Running from **internal ram**
STM32L475VG | 80MHz | 50 DMIPS |  0.62 DMIPS/MHz | Running from **internal flash**
STM32F411VE | 96MHz | 62 DMIPS |  0.65 DMIPS/MHz | Running from internal flash with **data/instruction cache on**
STM32F411VE | 96MHz | 47 DMIPS |  0.49 DMIPS/MHz | Running from internal ram with with **data/instruction cache off**
STM32F411VE | 96MHz | 39 DMIPS |  0.41 DMIPS/MHz | Running from internal flash with with **data/instruction cache off**

### Things to Notice

- Running code on external SDRAM with cache on vs cache off makes a huge difference (obviously) on the RT1052.
- The cache makes a big difference on the STM32H7/F7 chips even when running from internal flash and RAM. This is mainly due to the flash running at speeds lower than the CPU.
- The LPC4078 has better performance executing from RAM than from flash.

## Last Thing

The image at the top of the page shows the benchmark [dashboard](https://app.stratifylabs.co) for when I finally got the cache working on the STM32H743. To see all the data, you can login to the [Stratify Dashboard](https://app.stratifylabs.co) and browse to things.

[Discuss this](https://community.stratifylabs.co/t/dhrystone-benchmarking-on-mcus-stratify-labs/36).



