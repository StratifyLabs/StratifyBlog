---
categories:
- Device Tips
date: "2021-11-30"
layout: post
draft: false
tags:
- cpp
- programming
- microcontroller
- cortex-m
title: printf()-ers vs Code Steppers
---

- MCU Debugger Overview
  - ARM Block Diagram
  - Access the MCU debug unit
  - Breakpoints
  - Register and Variable Inspection
- printf() debugging (serial tracing)
  - UART output
    - can be SPI, I2C, USB
  - Causes a delay
  - formatting overhead
  - Allows for powerful insights
  - Design debug solutions to fit your problem
  - Statistical outputs
- Use MCU Debugger for non-invasive serial tracing
  - SWO for near-zero latency output (has an internal buffer)
  - Cycle counter
  - Watchpoints


