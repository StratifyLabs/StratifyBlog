---
categories:
- Device Tips
date: "2016-12-20"
layout: post
page_source: MicrocontrollerBlog
tagline: Embedded Design
tags:
- microcontroller
title: What's a Microcontroller
---

![Microcontroller](/images/microcontroller.svg)

A microcontroller is a microprocessor with integrated hardware peripherals (such as SPI, UART, I2C, ADC) and memory (usually RAM and flash). Compared to microprocessors, they boot instantly, consume less power and are easier to integrate into products but typically present firmware development challenges.

From a software development perspective, the big difference between a microcontroller and a microprocessor is that a microprocessor includes a memory management unit (MMU) which allows it to run operating systems that use virtual memory.  Virtual memory greatly eases the task of developing software and running applications. This paradigm allows software innovators all over the world to innovate on micrprocessor systems built by any manufacturer.

In contrast, microcontrollers run all code in the same address space (no MMU, no virtual memory) making it difficult for anyone but the OEM to build innovative software for the device. This is where Stratify OS comes in. Stratify OS is a microcontroller operating system that simulates an MMU using developer tools and a memory protection unit (MPU). Stratify OS allows microcontroller systems to use the microprocessor developer paradigam which makes firmware projects much easier. It also allows you to build microcontroller based products that are easy for any developer to innovate on.

Learn more about [Stratify OS](https://stratifylabs.co/Stratify-OS/).
