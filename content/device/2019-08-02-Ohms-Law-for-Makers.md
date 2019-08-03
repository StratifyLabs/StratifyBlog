---
categories:
- Device Tips
date: "2019-08-02"
layout: post
tags:
- microcontroller
- circuit
- EE for Makers
title: Ohm's Law for Makers
katex: true
viz: true
msc: true
---

> This is the first article in a series to teach you all the EE you need to know for designing microcontroller based circuits.

Ohm's law is a simple and handy formula when building electronic circuits. Voltage equals current times resistance.

$$ V=I \cdot R $$
 
Algebraic alternates are also useful.

$$ I = \frac{V}{R} $$

$$ R= \frac{V}{I} $$

I like to think about electrical systems like water flowing through pipes. The [Darcy–Weisbach](https://en.wikipedia.org/wiki/Darcy%E2%80%93Weisbach_equation) equation describes the pressure drop in a pipe.

$$ \Delta p = f_D \cdot \frac{\rho}{2} \cdot \frac{v^2}{D} \cdot L $$

\\( \Delta p \\) is the change in pressure (like voltage) while \\( f_D \\), \\( \rho \\), and \\( L \\) are related to resistance \\( R \\) to flow and \\( v^2 \\) and \\( D \\) are related to the flow \\( F \\) (like current). So if we redefine some terms, it looks a lot like ohms law.

$$ V = \Delta p $$
$$ I = \frac{v^2}{D} $$
$$ R = f_D \cdot \frac{\rho}{2} \cdot L $$
$$ V = I \cdot R $$

In this analogy, you can think of a battery as a little pump. Both pressure and voltage are a form of potential energy. Meaning there is potential for energy to be released but it will just sit there if it doesn't have anywhere to go. The battery keeps
the system pressurized until there is a closed path for the flow to return to the battery.

So let's look at our first circuit.

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

In this circuit electrons will be pushed by the potential energy in the voltage source from the negative terminal to the postive terminal. Because electrons have a negative charge and physically flow from minus to plus
the net positive flow of charge goes from positive to negative. Another thing to notice is that while the potential force travels around the circuit at the speed of light, the actually electrons only flow a few centimeters per second. 
This is similar to how a pipe become pressurized very quickly despite the fact that the water flows very slowly.

```msc
Caller->System: Sends 128-bit Random Number
Note right of System: Appends 128-bits to the number
System->Caller: 256-bit Random Number
Caller->System: SHA256(Secret Key, Random Number)
Note right of System: Validates token
Caller->System: SHA256(Random Number, Secret Key)
```


