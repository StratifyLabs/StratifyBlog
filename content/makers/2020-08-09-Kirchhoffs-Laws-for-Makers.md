---
categories:
- EE for Makers
date: "2020-08-06"
layout: post
tags:
- circuit
- EE for Makers
title: Kirchoff's Laws for Makers
katex: true
viz: true
msc: true
---

> This is the second article in a [series](/tags/EE-for-Makers/) to teach you all the EE you need to know for working with and designing microcontroller based circuits.

In 1845, German physicist Gustav Kirchhoff came up with two equations that generalized the application of Ohm's law. Kirchhoff's Current Law says the sum of all current going into a node must be zero. Kirchhoff's Voltage Law states that the sum of all voltages around a closed loop is zero. This seems a little off until you consider the direction of the current and the sign of the voltage.

## Kirchhoff's Current Law or KCL

<center>
<svg xmlns="http://www.w3.org/2000/svg" width="202" height="172" x="0" y="0"><text x="41" y="67" font-size="10" font-family="sans-serif" text-anchor="start" alignment-baseline="hanging">5V</text><text x="191" y="101" font-size="10" font-family="sans-serif" text-anchor="start" alignment-baseline="alphabetic" transform="rotate(270 191 101)">5k</text><text x="192" y="81" font-size="10" font-family="sans-serif" text-anchor="start" alignment-baseline="alphabetic" transform="rotate(270 192 81)">R1</text><text x="41" y="77" font-size="10" font-family="sans-serif" text-anchor="start" alignment-baseline="hanging"></text><line x1="31" y1="71" x2="11" y2="71" stroke-width="2" stroke="black"></line><line x1="26" y1="77" x2="16" y2="77" stroke-width="2" stroke="black"></line><line x1="31" y1="84" x2="11" y2="84" stroke-width="2" stroke="black"></line><line x1="26" y1="91" x2="16" y2="91" stroke-width="2" stroke="black"></line><line x1="21" y1="61" x2="21" y2="71" stroke-width="2" stroke="black"></line><line x1="21" y1="91" x2="21" y2="101" stroke-width="2" stroke="black"></line><text x="31" y="63" font-size="10" font-family="sans-serif" text-anchor="start" alignment-baseline="alphabetic" transform="rotate(90 31 63)">+</text><text x="36" y="101" font-size="10" font-family="sans-serif" text-anchor="end" alignment-baseline="hanging">-</text><line x1="171" y1="101" x2="166" y2="98" stroke-width="2" stroke="black"></line><line x1="166" y1="98" x2="176" y2="93" stroke-width="2" stroke="black"></line><line x1="176" y1="93" x2="166" y2="88" stroke-width="2" stroke="black"></line><line x1="166" y1="88" x2="176" y2="83" stroke-width="2" stroke="black"></line><line x1="176" y1="83" x2="166" y2="78" stroke-width="2" stroke="black"></line><line x1="166" y1="78" x2="176" y2="73" stroke-width="2" stroke="black"></line><line x1="176" y1="73" x2="171" y2="71" stroke-width="2" stroke="black"></line><line x1="171" y1="111" x2="171" y2="101" stroke-width="2" stroke="black"></line><line x1="171" y1="71" x2="171" y2="61" stroke-width="2" stroke="black"></line><line x1="171" y1="161" x2="21" y2="161" stroke-width="2" stroke="black"></line><line x1="21" y1="11" x2="21" y2="61" stroke-width="2" stroke="black"></line><line x1="21" y1="101" x2="21" y2="161" stroke-width="2" stroke="black"></line><line x1="171" y1="111" x2="171" y2="161" stroke-width="2" stroke="black"></line><line x1="171" y1="11" x2="21" y2="11" stroke-width="2" stroke="black"></line><line x1="171" y1="11" x2="171" y2="61" stroke-width="2" stroke="black"></line></svg>
</center>

KCL says the sum all all the current at a node on a circuit is zero. So first, what is a node? A node is any point on a wire in a schematic. It is more useful if the point has more than 2 places for current to go.


## Kirchhoff's Voltage Law or KVL



