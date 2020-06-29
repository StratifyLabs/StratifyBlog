---
categories:
- Device Tips
date: "2020-05-24"
layout: post
tags:
- microcontroller
- cpp
- programming
draft: true
title: Maximizing the Value of the STM32H750
---

The Stratify Toolbox is powered by the mighty and inexpensive STM32H750. Mighty because it runs at 480MHz and has 1MB of internal SRAM. Inexpensive because it costs about $4 each in quantities of over 1000. It's biggest problem is that it only has 128KB of internal flash which isn't nearly enough to run all the features needed on the Toolbox.

Fortunately, there is an easy way to expand the flash memory. Recently more and more microcontrollers are allowing system designers to extend the internal flash with external QSPI flash. Normally, that would mean a big performance hit (doesn't do much good to clock the CPU at 480MHz if you can't feed it instructions that fast). But the STM32H750 is an ARM Cortex M7 with an internal cache.

The cache in the ARM Cortex M7 makes the device perform almost the same whether executing from flash or RAM. Additionally, you can put time critical code in tightly coupled instruction memory (TCIM). I ran a standard dhrystone application to see the difference.

- All code in RAM: 464 DMIPS
- OS in QSPI/TCIM, Application in RAM: 522 DMIPS






