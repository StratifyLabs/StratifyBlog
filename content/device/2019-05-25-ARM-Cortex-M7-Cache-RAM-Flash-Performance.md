---
categories:
- Device Tips
date: "2019-05-25"
layout: post
tags:
- microcontroller
- programming
- cortex-m
- benchmark
title: ARM Cortex M7 Cache, RAM, and Flash Performance
chart: true
---

I recently ran som Dhyrstone benchmarking tests on an ARM Cortex M7 core to see how the tests performed from various memory locations. The ARM Cortex M7 is a microcontroller core contained in microcontrollers from various manufacturers. In my case, I used an STM32F723E from STMicroelectronics. The ARM Cortex M7 includes a data and instruction cache that can be used to improve performance. In the case of the STM32F723E, there are 8KB of each type.

Because most microcontrollers have integrated flash memory and RAM, I was curious how much performance could be boosted with cache. When I dug a little deeper, I found it can be boosted a lot. I discovered a few reasons why:

- Memory buses don't always run at the same speed as the core
- Fetching memory can sometimes take multiple clock cycles
- Bus contention (from DMA or other hardware) can delay data and instructions from getting to the core

The cache overcomes these problems by tightly coupling the cache to the core processor in the Cortex M7.

## The Setup

I performed the tests using an off-the-shelf 32F723E-DISCO development board from STMicroelectronics running StratifyOS. It includes:

- 216MHz Core CPU Speed
- 512KB Internal Flash memory
- 64KB Tightly Coupled RAM
- 176KB Internal RAM
- 512KB External RAM on a 16-bit data bus

If you have a 32F723E-DISCO board, you can install Stratify OS using the following commands after you have [installed the sl command line tool]().

```
sl os.bootstrap:bootloader
sl os.bootstrap:os
```

## The Tests

Once Stratify OS is installed, you can run the [dhrystone]({{< ref "2019-05-20-Dhrystone-Benchmarking-on-MCUs.md" >}}) application using the following commands:

```
sl bench.test:id=QpXcn3w2P1YUcatvAZZd # runs in flash
sl bench.test:id=QpXcn3w2P1YUcatvAZZd,ram
sl bench.test:id=QpXcn3w2P1YUcatvAZZd,ram,tightlycoupled
sl bench.test:id=QpXcn3w2P1YUcatvAZZd,ram,external
```

## The Results

```chart
    {
    "type": "bar",
        "data": {
            "labels": ["DMIPS Cache On", "DMIPS Cache Off"],
            "datasets": [
            {
                "label": "Flash",
                "data": [
                    245,
                    49
                    ],
                "backgroundColor":"orange",
                "borderColor":"orange"
            },
            {
                "label": "RAM",
                "data": [
                    245,
                    69
                    ],
                "backgroundColor":"blue",
                "borderColor":"blue"
            },
            {
                "label": "External RAM",
                "data": [
                    245,
                    69
                    ],
                "backgroundColor":"red",
                "borderColor":"red"
            },
            {
                "label": "Tightly Coupled RAM",
                "data": [
                    239,
                    217
                    ],
                "backgroundColor":"green",
                "borderColor":"green"
            }
            ]
        }
    }
```

Memory | Cache On | Cache Off | Cache Speed Up
-------|----------------|-----------------|---------------
Flash | 245 DMIPS | 49 DMIPS | **5x**
RAM | 245 DMIPS | 69 DMIPS | **5x**
External RAM | 245 DMIPS | 69 DMIPS | **5x**
Tightly Coupled RAM | 239 DMIPS | 217 DMIPS | **1.1x**

## The Conclusion

The big take away is that applications running in external RAM run just as fast as applications running in any other memory as long as the cache is on. Not surprisinly, execution from tightly coupled memory was the least affected by the cache.
