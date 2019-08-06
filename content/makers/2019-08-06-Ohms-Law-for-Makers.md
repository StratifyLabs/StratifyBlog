---
categories:
- EE for Makers
date: "2019-08-06"
layout: post
tags:
- circuit
- EE for Makers
title: Ohm's Law for Makers
katex: true
viz: true
msc: true
---

> This is the first article in a [series](/tags/EE-for-Makers/) to teach you all the EE you need to know for working with and designing microcontroller based circuits.

Ohm's law is a simple and handy formula when building electronic circuits. Voltage equals current times resistance.

$$ V=I \cdot R $$
 
Algebraic alternates are also useful.

$$ I = \frac{V}{R} $$
$$ R= \frac{V}{I} $$

## Pressure Analogy

I like to think about electrical systems like water flowing through pipes. The [Darcy–Weisbach](https://en.wikipedia.org/wiki/Darcy%E2%80%93Weisbach_equation) equation describes the pressure drop in a pipe.

$$ \Delta p = f_D \cdot \frac{\rho}{2} \cdot \frac{v^2}{D} \cdot L $$

\\( \Delta p \\) is the change in pressure (like voltage) while \\( f_D \\), \\( \rho \\), and \\( L \\) are related to resistance \\( R \\) to flow and \\( v^2 \\) and \\( D \\) are related to the flow \\( F \\) (like current). So if we redefine some terms, it looks a lot like ohms law.

$$ V = \Delta p $$
$$ I = \frac{v^2}{D} $$
$$ R = f_D \cdot \frac{\rho}{2} \cdot L $$
$$ V = I \cdot R $$

In this analogy, you can think of a battery as a little pump. Both pressure and voltage are a form of potential energy. Meaning there is potential for energy to be released but it will just sit there if it doesn't have anywhere to go. The battery keeps
the system pressurized until there is a closed path for the flow to return to the battery.

## Our first Circuit

<center>
<svg version="1.1" width="260" height="200" xmlns="http://www.w3.org/2000/svg">
	<line x1="240" y1="40" x2="240" y2="50" style="stroke:rgb(0, 0, 0);stroke-linecap:square;stroke-width:2" />
	<line x1="240" y1="50" x2="240" y2="60" style="stroke:rgb(0, 0, 0);stroke-linecap:square;stroke-width:2" />
	<line x1="240" y1="30" x2="240" y2="40" style="stroke:rgb(0, 0, 0);stroke-linecap:square;stroke-width:2" />
	<line x1="240" y1="50" x2="240" y2="80" style="stroke:rgb(0, 0, 0);stroke-linecap:square;stroke-width:2" />
	<line x1="240" y1="120" x2="240" y2="150" style="stroke:rgb(0, 0, 0);stroke-linecap:square;stroke-width:2" />
	<path d="M 240,80 L 240,82 L 233,85 L 247,91 L 233,97 L 247,103 L 233,109 L 247,115 L 240,118 L 240,120" style="fill-opacity:0;fill:rgb(0, 0, 0);stroke:rgb(0, 0, 0);stroke-linecap:square;stroke-width:2" />
	<text x="226" y="100" style="font-family:Arial;font-size:11px;text-anchor:end" dy=".3em">1 kΩ</text>
	<line x1="240" y1="130" x2="240" y2="180" style="stroke:rgb(0, 0, 0);stroke-linecap:square;stroke-width:2" />
	<line x1="40" y1="30" x2="40" y2="89" style="stroke:rgb(0, 0, 0);stroke-linecap:square;stroke-width:2" />
	<ellipse cx="40" cy="105" rx="16" ry="16" style="fill-opacity:0;fill:rgb(0, 0, 0);stroke:rgb(0, 0, 0);stroke-width:2" />
	<line x1="40" y1="121" x2="40" y2="180" style="stroke:rgb(0, 0, 0);stroke-linecap:square;stroke-width:2" />
	<path d="M 40,105 M 40,95 L 40,103 M 36,99 L 44,99 M 36,113 L 44,113" style="fill-opacity:0;fill:rgb(0, 0, 0);stroke:rgb(0, 0, 0);stroke-linecap:square;stroke-width:2" />
	<line x1="40" y1="30" x2="240" y2="30" style="stroke:rgb(0, 0, 0);stroke-linecap:square;stroke-width:2" />
	<line x1="40" y1="180" x2="240" y2="180" style="stroke:rgb(0, 0, 0);stroke-linecap:square;stroke-width:2" />
</svg></center>

In this circuit, electrons will be pushed by the potential energy in the voltage source from the negative terminal to the postive terminal. Because electrons have a negative charge and physically flow from minus to plus, the net positive flow of charge goes from positive to negative. Another thing to notice is that while the potential force travels around the circuit at the speed of light, the actual electrons only flow a few centimeters per second. This is similar to how a pipe become pressurized very quickly, but the water flows very slowly.

Applying Ohm's law to this circuit is simple (let's say the voltage source is 5V).

$$ V = 5V $$
$$ R = 1000 Ohms $$
$$ I = \frac{5V}{1000Ohms} = 0.005 A $$

## Units Never Lie

When you are doing these types of calculations, it is really helpful to consider the units. The calculation will never be correct if the units don't work out. For example, an ampere (amp) is defined as coulombs per seconds \\(\frac{C}{s}\\). Voltage is defined as joules (a measure of energy) per coulomb \\(\frac{J}{C}\\). Resistance is measured in ohms (\\(\Omega\\)) which has to be:

$$ \frac{J}{C} = \frac{C}{s} \cdot \Omega $$
$$ \frac{\frac{J}{C}}{\frac{C}{s}} = \Omega $$
$$ \frac{Js}{C^2} = \Omega $$

## Power Consumption Equation

For any other calcuations, the above units will always hold true. We can see this when we derive the equation for calculating electric power knowing a Watt is defined as one \\(\frac{J}{s}\\).

$$ P = \frac{J}{s} = \frac{J}{C} \cdot \frac{C}{s} $$
$$ P = V \cdot I $$

In the example above, the circuit consumes:

$$ P = 5V \cdot 0.005A = 0.025 W $$

There are also some useful algebraic identities using Ohm's law with the power equation.

$$ P = \frac{V^2}{R} $$
$$ P = I^2 \cdot R $$

## Conclusion

Ohm's law defines the relationship between voltage, current and resistance while electric power is the product of voltage and current. In the next post, we will go over Kirchoff's voltage and current laws which are used to apply Ohm's law to more complex circuitry.


