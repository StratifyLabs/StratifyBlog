---
categories:
- Device Tips
date: "2021-05-08"
layout: post
draft: false
tags:
- microcontroller
- cpp
- programming
title: STM32 UART DMA Circular Buffer
---

The STM32 series of MCUs include a UART as well as a DMA. I am going to explain how I use the UART and DMA in circular mode. The DMA will write to a FIFO buffer and the processor will then read the FIFO buffer. My approach as minimal processor overhead for processing UART input because the DMA does most the work.

## The Setup

For this approach we need:

- A FIFO buffer implementation
- UART DMA in circular mode
- UART idle interrupt enabled

Here is how it will work:

> If you need to get familiar with how a FIFO buffer works with a `head` and a `tail`, [please read more]().

The processor will configure the UART DMA to read in circular mode and start reading. The DMA half and full interrupts will update the FIFO `head`. The UART idle interrupt will also update the FIFO `head`. The processor will update the FIFO `tail` when reading. If the processor doesn't read the data before the DMA circular read starts over, the tail is advanced and the oldest data is lost.

