---
categories:
- Device Tips
date: "2021-12-30"
layout: post
draft: true
tags:
- cpp
- programming
- microcontroller
- cortex-m
title: UART FIFO with DMA on STM32
---

- STM32 lacks a straightforward way to use the DMA with the UART.
- There is an elegant solution hiding in there somewhere
- The key is to use the Idle interrupt with Circular DMA on top of a FIFO buffer



