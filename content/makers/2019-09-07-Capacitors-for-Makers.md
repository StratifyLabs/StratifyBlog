---
categories:
- EE for Makers
date: "2019-09-07"
layout: post
draft: false
tags:
- circuit
- EE for Makers
title: Capacitors for Makers
katex: true
---

> This is the fourth article in a [series](/tags/EE-for-Makers/) to teach you all the EE you need to know for working with and designing microcontroller based circuits.

Capacitors are very common in designs with microcontrollers. Though the math is more complex than with just using resistors, a few rules of thumb will let you understand what most capacitors are doing in microcontroller based circuits.

## Thinking About Capacitance

Physically a capacitor contains two parallel plates that are separated by a dielectric material which is an insulator that can be electrically polarized. The geometry of the plates and the type of dielectric material determine the capacitance. Larger plates and higher dieletric permittivity mean more capacitance.

When you apply a voltage to a capacitor, a charge builds up between the two plates--somewhat similar to how charge builds up between the ground and clouds during a storm and sometimes discharges as lightning. The energy is stored in an electric field (later we will talk about how inductors store energy in a magnetic field). That energy can then be used in the circuit.

> If you like the pressurized water system analogy, you can think of a capacitor as a tank that holds pressurized water and is controlled by a special valve that only accepts or releases pressure when the external pressure changes.

## Math and Units

Capacitance is measured in Farads which is a ratio of charge (coulombs) per unit voltage (volts).

$$ farad = \frac{coulombs}{volt} $$
$$ F = \frac{C}{V} $$

Also remember current is flow of charge (coulombs) per second. And a volt is the amount of energy (joules) per unit of charge.

$$ amp = \frac{coulombs}{s} $$
$$ A = \frac{C}{s} $$
$$ volt = \frac{joules}{coulomb} $$

## Capacitors and Ohm's Law

Ohm's law doesn't directly apply to capacitors because they are not a purely resistive device. When a device is not purely resistive, it is said to have impedance rather than resistance. There are many really complicated ways to model capacitors in circuits but for most microcontroller circuits they are not needed.

Ohm's law defines the voltage drop as a function of current and resistance. 

$$ V = I \cdot R $$

For capacitors, the simplest similar equation defines the current through a capacitor.

$$ I = C \cdot \frac{dV}{dt} $$



If we remember back to Ohm's law, we know


## Energy Storage


The amount of energy a capacitor can store is defined by:

$$ E = \frac{1}{2} \cdot C \cdot V^2 (C is in farads) $$


