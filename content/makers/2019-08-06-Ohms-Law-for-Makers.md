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

George Ohm was a German physicist, mathematician and one of the first "makers". He built some equipment that allowed him to show the proportional relationship between voltage and current, known today as Ohm's law. Ohm's law is a simple and handy formula when building electronic circuits. It is: voltage equals current times resistance.

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

{{< html >}}
<center>
<svg xmlns="http://www.w3.org/2000/svg" width="202" height="172" x="0" y="0"><text x="41" y="67" font-size="10" font-family="sans-serif" text-anchor="start" alignment-baseline="hanging">5V</text><text x="191" y="101" font-size="10" font-family="sans-serif" text-anchor="start" alignment-baseline="alphabetic" transform="rotate(270 191 101)">5k</text><text x="192" y="81" font-size="10" font-family="sans-serif" text-anchor="start" alignment-baseline="alphabetic" transform="rotate(270 192 81)">R1</text><text x="41" y="77" font-size="10" font-family="sans-serif" text-anchor="start" alignment-baseline="hanging"></text><line x1="31" y1="71" x2="11" y2="71" stroke-width="2" stroke="black"></line><line x1="26" y1="77" x2="16" y2="77" stroke-width="2" stroke="black"></line><line x1="31" y1="84" x2="11" y2="84" stroke-width="2" stroke="black"></line><line x1="26" y1="91" x2="16" y2="91" stroke-width="2" stroke="black"></line><line x1="21" y1="61" x2="21" y2="71" stroke-width="2" stroke="black"></line><line x1="21" y1="91" x2="21" y2="101" stroke-width="2" stroke="black"></line><text x="31" y="63" font-size="10" font-family="sans-serif" text-anchor="start" alignment-baseline="alphabetic" transform="rotate(90 31 63)">+</text><text x="36" y="101" font-size="10" font-family="sans-serif" text-anchor="end" alignment-baseline="hanging">-</text><line x1="171" y1="101" x2="166" y2="98" stroke-width="2" stroke="black"></line><line x1="166" y1="98" x2="176" y2="93" stroke-width="2" stroke="black"></line><line x1="176" y1="93" x2="166" y2="88" stroke-width="2" stroke="black"></line><line x1="166" y1="88" x2="176" y2="83" stroke-width="2" stroke="black"></line><line x1="176" y1="83" x2="166" y2="78" stroke-width="2" stroke="black"></line><line x1="166" y1="78" x2="176" y2="73" stroke-width="2" stroke="black"></line><line x1="176" y1="73" x2="171" y2="71" stroke-width="2" stroke="black"></line><line x1="171" y1="111" x2="171" y2="101" stroke-width="2" stroke="black"></line><line x1="171" y1="71" x2="171" y2="61" stroke-width="2" stroke="black"></line><line x1="171" y1="161" x2="21" y2="161" stroke-width="2" stroke="black"></line><line x1="21" y1="11" x2="21" y2="61" stroke-width="2" stroke="black"></line><line x1="21" y1="101" x2="21" y2="161" stroke-width="2" stroke="black"></line><line x1="171" y1="111" x2="171" y2="161" stroke-width="2" stroke="black"></line><line x1="171" y1="11" x2="21" y2="11" stroke-width="2" stroke="black"></line><line x1="171" y1="11" x2="171" y2="61" stroke-width="2" stroke="black"></line></svg>
</center>
{{< /html >}}

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


