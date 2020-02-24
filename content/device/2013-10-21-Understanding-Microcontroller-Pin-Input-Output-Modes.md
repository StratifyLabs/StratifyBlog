---
categories:
- Device Tips
date: "2013-10-21"
layout: post
page_source: MicrocontrollerBlog
tagline: Embedded Design
tags:
- microcontroller
- c/c++
- programming
- circuit
- popular
title: Understanding Microcontroller Pin Input/Output Modes
katex: true
---

GPIO (aka General Purpose input/output) is the simplest of microcontroller IO. Even so, GPIO comes in various types and varieties. There is input, output, pull-up, pull-down, push-pull, high-drive, open-drain and more. We will take a look at each of these and cut through the confusion so you know exactly how you need to configure your pins.

### Input Modes

GPIO input modes include

- high impedance
- pull-up
- pull-down
- repeater
- hysteresis (not usually optional, but we will talk about it anyway)

####  Floating, High Impedance, Tri-Stated

Floating, high impedance and tri-stated are three terms that mean the same thing: the pin is just flopping in the breeze. Its state is indeterminate unless it is driven high or low externally. You only want to congfigure a pin as floating if you know it will be driven externally. Otherwise, configure the input using pulling resistors.

> **A Word on Impedance**. Impedance is very similar to resistance but considers how a circuit reacts when a changing voltage is applied. A resistor only has resistance. But [inductors]({{< relref "2019-09-25-Inductors-for-Makers.md" >}}) and [capacitors]({{< relref "2019-09-17-Capacitors-for-Makers.md" >}}) have impedance (resistance + reactance). Now you know!

####  Pull Up/Down

If an input is configured with an internal pull-up, it will be high unless it is externally driven low. Pull-down inputs do the opposite (<small> they're low unless driven high</small>). Some GPIO pins also support changing the pull-up and pull-down settings dynamically using repeater mode (but truth be told, I have never configured a pin that way).

> [Read more about pull-up and pull-down resistors.]({{< ref "2013-10-25-Using-Pull-Up-and-Pull-Down-Resistors.md" >}})

#### Repeater

When a GPIO is configured in repeater mode, the pull-up is enabled when the pin is driven high, and the pull-down is enabled when the pin is driven low.  If nothing is driving the pin, the pin will retain its last known state (so I guess "repeater" isn't just a clever name). In some cases, this can prevent a pin from floating.

> You might be wondering why pins shouldn't be floating. When pins are floating, current can leak in and out of the pin. \\( leaky+current = bad \\). So, don't leave pins floating.

#### Hysteresis

Virtually all GPIO inputs use a principle called hysteresis to prevent spurious changes in state when an input changes. I think of hysteresis of having a low threshold to go low and a high threshold to go high. So if you are somewhere in the middle, nothing changes. You can see this in the graph below on where `A` (with no hysteresis) has some state changes on that `B` (with hysteresis) filters out.

![Hysteresis Graph](/images/smitt_hysteresis_graph.svg)

The low threshold can be found in the datasheet in the "Electrical Characteristics" section as {{< katex inline >}}V_{IL}{{< /katex >}} and the high threshold is {{< katex inline >}}V_{IH}{{< /katex >}}.

### Output Modes

#### Push-Pull

A push-pull output is another aptly-name GPIO mode. The pin as the ability to "push" the signal high or "pull" it low. It does this using a pair of complementary transistors. You have probably heard of "CMOS" technology. The C stands for "complementary". A [CMOS]({{< relref "2013-10-14-How-Microcontrollers-Work.md#cmos" >}}) device has a pushing transistor [(PMOS) and a pulling transistor (NMOS)]({{< relref "2013-10-14-How-Microcontrollers-Work.md#field-effect-transistors-fets" >}}).

In the circuit below where `I` is the input and `O` is the output

- when `I` is 1, `PMOS` is **off** and `NMOS` is **on** which makes `O` be 0
- when `I` is 0, `PMOS` is **on** and `NMOS` is **off** which makes `O` be 1 (kanobi)

{{< html >}}
<center>
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="371px" height="443px" viewBox="-0.5 -0.5 371 443"><defs/><g><path d="M 247 190 L 247 260" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 180 140 L 110 140 L 110 210 L 30 210 L 20 210" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><ellipse cx="232.25" cy="140" rx="42.75" ry="40.90909090909091" fill="#ffffff" stroke="#000000" pointer-events="none"/><path d="M 222.75 121.82 L 246.5 121.82 L 246.5 90 M 222.75 158.18 L 246.5 158.18 L 246.5 190 M 219.9 119.09 L 219.9 160.91 M 180 140 L 216.1 140" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><ellipse cx="218" cy="140" rx="1.9" ry="1.8181818181818181" fill="transparent" stroke="#000000" pointer-events="none"/><path d="M 222.75 117.27 L 222.75 162.73" fill="#ffffff" stroke="#000000" stroke-width="1.82" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(209.5,197.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="35" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">PMOS</div></div></foreignObject><text x="18" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">PMOS</text></switch></g><path d="M 180 310 L 110 310 L 110 210 L 30 210" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 247 260 L 247 210 L 350 210" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 247 360 L 247 432 L 200 432" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><ellipse cx="232.25" cy="310" rx="42.75" ry="40.90909090909091" fill="#ffffff" stroke="#000000" pointer-events="none"/><path d="M 222.75 291.82 L 246.5 291.82 L 246.5 260 M 222.75 328.18 L 246.5 328.18 L 246.5 360 M 219.9 289.09 L 219.9 330.91 M 180 310 L 219.9 310" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 222.75 287.27 L 222.75 332.73" fill="#ffffff" stroke="#000000" stroke-width="1.82" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(209.5,367.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="36" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">NMOS</div></div></foreignObject><text x="18" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">NMOS</text></switch></g><ellipse cx="10" cy="210" rx="10" ry="10" fill="#ffffff" stroke="#000000" pointer-events="none"/><g transform="translate(8.5,203.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="3" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 4px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">I</div></div></foreignObject><text x="2" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">I</text></switch></g><ellipse cx="360" cy="210" rx="10" ry="10" fill="#ffffff" stroke="#000000" pointer-events="none"/><g transform="translate(355.5,203.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="9" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 10px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">O</div></div></foreignObject><text x="5" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">O</text></switch></g><path d="M 200 10 L 247 10 L 247 90" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><ellipse cx="190" cy="10" rx="10" ry="10" fill="#ffffff" stroke="#000000" pointer-events="none"/><g transform="translate(186.5,3.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="7" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 8px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">1</div></div></foreignObject><text x="4" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">1</text></switch></g><ellipse cx="190" cy="432" rx="10" ry="10" fill="#ffffff" stroke="#000000" pointer-events="none"/><g transform="translate(186.5,425.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="7" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 8px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">0</div></div></foreignObject><text x="4" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">0</text></switch></g></g></svg>
</center>
{{< /html >}}


#### Open-Drain

To understand what open drain means, we need to look at the anatomy of a MOSFET transistor. The MOSFET (NMOS in this case) has three terminals.

- Drain (spoiler-alert this is the "drain" part of "open-drain")
- Gate
- Source

{{< html >}}
<center>
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="371px" height="253px" viewBox="-0.5 -0.5 371 253"><defs/><g><path d="M 180 120 L 110 120 L 110 20 L 30 20" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 247 70 L 247 20 L 350 20" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 247 170 L 247 242 L 200 242" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><ellipse cx="232.25" cy="120" rx="42.75" ry="40.90909090909091" fill="#ffffff" stroke="#000000" pointer-events="none"/><path d="M 222.75 101.82 L 246.5 101.82 L 246.5 70 M 222.75 138.18 L 246.5 138.18 L 246.5 170 M 219.9 99.09 L 219.9 140.91 M 180 120 L 219.9 120" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 222.75 97.27 L 222.75 142.73" fill="#ffffff" stroke="#000000" stroke-width="1.82" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(209.5,177.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="36" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">NMOS</div></div></foreignObject><text x="18" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">NMOS</text></switch></g><ellipse cx="10" cy="20" rx="10" ry="10" fill="#ffffff" stroke="#000000" pointer-events="none"/><g transform="translate(8.5,13.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="3" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 4px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">I</div></div></foreignObject><text x="2" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">I</text></switch></g><ellipse cx="360" cy="20" rx="10" ry="10" fill="#ffffff" stroke="#000000" pointer-events="none"/><g transform="translate(355.5,13.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="9" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 10px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">O</div></div></foreignObject><text x="5" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">O</text></switch></g><ellipse cx="190" cy="242" rx="10" ry="10" fill="#ffffff" stroke="#000000" pointer-events="none"/><g transform="translate(186.5,235.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="7" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 8px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">0</div></div></foreignObject><text x="4" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">0</text></switch></g><g transform="translate(260.5,3.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="29" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 30px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">Drain</div></div></foreignObject><text x="15" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">Drain</text></switch></g><g transform="translate(136.5,103.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="26" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 27px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">Gate</div></div></foreignObject><text x="13" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">Gate</text></switch></g><g transform="translate(255.5,173.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="38" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 39px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">Source</div></div></foreignObject><text x="19" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">Source</text></switch></g></g></svg>
</center>
{{< /html >}}

On an open-drain pin, there is no "push" transistor. So

- when `I` is 1, `O` is pulled to 0
- when `I` is 0, `O` is "open" (get it? The drain is open as in open-drain pin)

If you recall about 2 minutes ago, we don't want to leave pins floating so in most cases open-drain pins will have an external pullup resistor. If you are really tricky (of course, you are), you can configure the MCU's pull-up resistor and use the pin in open-drain output mode.

One of the cool things about open-drain pins is that you can have more than one open drain output connected to an MCU input. If any of the open-drains are pulling the line to zero, the input will be zero. Since none are ever pushing, you won't have any digital infighting (which is worse than floating pins). This is why many sensors have open-drain outputs for signaling events. 
You can connect a bunch of them to one microcontroller interrupt then use the [I2C (or other bus)]({{< ref "2019-07-25-My-Favorite-Serial-Bus.md" >}}) to see which one is active.

#### High Drive

When a GPIO has high drive capability, it is just a push-pull pin that can source or sink more current than usual. A typical push-pull output is able to source/sink around +/-8ma where a high drive output may be up to +/-40ma. Again, that "Electrical Characteristics" section of the datasheet is your goto source for the details.

Understanding the current capabilities of pins is important if you are trying to drive LEDs or do anything that is more than just sending data back and forth. If you want [a nice bright LED indicator]({{< ref "2013-10-24-Using-LEDs-in-Embedded-Designs.md" >}}), you will use about 20ma which is more than a run-of-the-mill GPIO pin can provide.

### The End

For a brief re-cap, GPIO is the simplest IO on microcontrollers.  However, it turns out it isn't *that* simple. You will want to be deliberate about setting input pins with (or without) pulling resistors. For outputs, remember push-pull vs open-drain and make sure you have enough drive current for the task at hand.

> Readability changes published on September 11 (**never forget**), 2019
