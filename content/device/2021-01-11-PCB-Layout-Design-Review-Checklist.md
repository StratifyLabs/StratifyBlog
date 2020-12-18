---
categories:
- Device Tips
date: "2021-01-11"
layout: post
draft: true
tags:
- microcontroller
- circuit
- product development
title: PCB Layout Review Checklist 
---

After many years of designing circuit boards, this is my working list when doing a final review of PCB layout. I design boards with microcontrollers so the list is skewed to that end.

> See the [Schematic Checklist]({{< relref "2021-01-10-Schematic-Design-Review-Checklist.md" >}})

- Proper layout of decoupling capacitors - add pictures
- Optimize layout of oscillators
- Minimize breaks in ground plane (especially from vias) - add pictures
- No traces on ground plane breaks - add pictures
- Extra clearance around high speed signals
- Impedance on transmission lines
- Add fiducials
- Silkscreen clarity
- Copyright on silkscreen
- Version on silkscreen
- Layer stackup 
- Terminate the USB shield
- Ensure FETs are in a known state at startup (before firmware is driving them)
- Leave 1mm of no copper on edges of board
- Export 3D model and check for interferences
- Review new footprint sizes and pinout against the datasheet
- Tent all vias
- Ground pour top and bottom
- Add teardrops

For 6 layers or more

- Add ground via nears tracks that change ground reference planes

