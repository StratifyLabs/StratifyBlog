---
categories:
- Device Tips
date: "2021-01-05"
layout: post
draft: false
tags:
- microcontroller
- circuit
- product development
title: Schematic Design Review Checklist 
---

> Warning. This is a work in progress.

After many years of designing circuit boards, this is my working list when doing a final review of schematics. I design boards with microcontrollers so the list is skewed to that end.

> See the [PCB Layout Checklist]({{< relref "2021-01-05-PCB-Layout-Design-Review-Checklist.md" >}})

- Add decoupling capacitors to power pins
- Ensure I2Cs lines have pullup resistors
- Check and double check UART TX/RX lines for proper source/sink
- Review MCU peripheral availability by writing firmware drivers
- Terminate the USB shield
- Check Sheet numbering
- Check Copyright
- Check Version
- Ensure FETs are in a known state at startup (before firmware is driving them)
- Add a test point to every node (if practical)
- Low speed lines going off the PCB should have RF filtering (ferrites)


