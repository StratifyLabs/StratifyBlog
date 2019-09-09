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

$$ farad = \frac{coulombs}{volt} \space \to \space C = \frac{Q}{V} $$

Ohm's law doesn't directly apply to capacitors because they are not a purely resistive device. Because capacitors don't simple burn off energy as heat but store and release energy, they actively "react" to energy flow in the circuit rather than passively absord energy. When a device actively reacts it has impedance rather than just resistance. Impedance is a combined measure of resistance and reactance.

For AC circuits, capacacitors have complex effects, but in DC circuits you can understand capacitors by just learning a few simple rules.

## Capacitors in DC Circuits

The current through a capacitor is described by this equation.

$$ I = C \cdot \frac{dV}{dt} $$

\\(\frac{dV}{dt}\\) is a calculus term meaning the change in voltage divided by the change in time. If you were to plot voltage over time, \\(\frac{dV}{dt}\\) would be the instantaneous slope at any point. If the voltage is constant over time (as in with DC circuits), the current through the capacitor is zero.

When voltage is first applied to a capacitor, the capacitor charge up. But after it is charged it can be modeled by infinite resistance (no current flow).

EQUIVALENT CIRCUIT

## Decoupling Capacitors

## Filtering Capacitors

## DC Blocking Capacitors


