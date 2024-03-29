---
categories:
- Device Tips
date: "2016-12-14"
layout: post
page_source: PcbBlog
tagline: Understanding Current and Voltage
tags:
- microcontroller
- circuit
title: Ohms Law and Microcontroller GPIO
katex: true
---

![Ohms Law Circuit](/images/ohms-law-VIR-circuit.svg)

[Ohm's law]({{< relref "2019-08-06-Ohms-Law-for-Makers.md" >}}) is the basis for building simple DC circuits.  Many microcontroller applications are built on this simple equation. Ohm's law states the voltage drop across a resistor is equal to the current through the resistor multiplied by the resistance or {{< katex inline >}}V=IR{{< /katex >}}.

Let's analyze the simplest of circuits: a power supply with a load resistor (shown above). In this case, assume the voltage is 3.0V and R is 30 Ohms.  Increasing the resistance causes a decrease in current and vice versa.  In addition to specifying the resistor value, you also need to ensure the resistor's power rating is sufficient for the current passing through it.

Power is calculated as the voltage times current.  There are a few algebraic ohm's law substitutions in the diagram below.

{{< katex >}} P=VI {{< /katex >}}
{{< katex >}} P=\frac{V^2}{R} {{< /katex >}}
{{< katex >}} P=I^2 \cdot R {{< /katex >}}

For the case above, the voltage is 3.0 volts and the current is 0.1A resulting in 0.3W of power being dissipated in the resistor. A typical 1/4W resistor is insufficient in this case. A 0.5W or 1W resistor is a better choice for this application. A good rule of thumb is to calculate the max power and then double it.

Microcontroller Input
---------------------

[Microcontroller inputs can take the form of floating, internal pull-up, and internal pull-down pins]({{< relref "2013-10-21-Understanding-Microcontroller-Pin-Input-Output-Modes.md" >}}). For this discussion, we will consider a floating--sometimes called high impedance--input.

High impedance in Ohm's law parlance is a fancy way of saying a very high resistor value which equates to a very low current.  Consider the diagram below.

![Ohms law external pullup](/images/ohms-law-external-pullup.svg)

The above diagram shows a microcontroller input pin (IO) and an external pullup resistor.  When the input pin is floating, it can be modeled as a resistor with a very high value (say {{< katex inline >}}20M \Omega {{< /katex >}}) connected to ground.  Using Ohm's law, a very small amount of current will flow through the external resistor into the microcontroller pin.  The voltage at the pin will be equal to `3.0V` minus the tiny voltage dropped on the external resistor.  In practical terms, the voltages are the same.

When the switch is closed, any current that was flowing into the pin will flow through the switch.  The voltage at the pin will be zero because it is connected directly to ground. The current through the resistor can be calculated using Ohm's law.

A more complicated example involves a pullup to a voltage that differs from the microcontroller source voltage.  Let's say we have a 5V digital signal, but the microcontroller can only handle 3V.  We can use Ohm's law to drop some of the voltage before it enters the microcontroller.

![Ohms law voltage divider](/images/ohms-law-voltage-divider.svg)

In this case let's say {{< katex inline >}}R_1{{< /katex >}} is {{< katex inline >}}10K \Omega {{< /katex >}}. We can calculate a value for {{< katex inline >}}R_2{{< /katex >}} to ensure the input sees a maximum of {{< katex inline >}}3.0V{{< /katex >}}. To acheive this, the voltage drop on R1 needs to be {{< katex inline >}}2.0V{{< /katex >}} and using Ohm's law we get {{< katex inline >}}2.0V = I_1 \cdot 10K \Omega {{< /katex >}} where solving for {{< katex inline >}}I_1{{< /katex >}} gives {{< katex inline >}}0.2mA {{< /katex >}}. Since very little current will go into the IO pin, the current through both resistors if practically the same ({{< katex inline >}}I_1 = I_2 {{< /katex >}}). Using that we can use Ohm's law to calculate a value for R2: {{< katex inline >}}3.0V = 2mA \cdot R_2{{< /katex >}}and solving gives us {{< katex inline >}}R_2 = 15K \Omega {{< /katex >}}.

> This circuit is also called a [voltage divider]({{< relref "2019-08-24-Kirchhoffs-Laws-for-Makers.md#voltage-divider-circuit" >}}).

Microcontroller Output
---------------------

Using a microcontroller to drive an LED is a good illustration of using Ohm's law.  The diagram below shows two approaches.

![Ohms Law LED Driver](/images/ohms-law-led-driver.svg)

Let's look at how Ohm's law applies here.  The LED has a forward voltage drop that is a characteristic of semiconductor physics.  The resistor that is in line with the LED limits the amount of current running through the LED.  Assuming the LED voltage drop is 1.0V and VCC is 3.0V, we drop 2.0V through the resistor. 2.0V = I*R. We can set a value of R to control the max current through the LED. For example, if R is 100 Ohms, then the current through both the LED and the resistor will be 20mA.

Without the resistor, the microcontroller will push as much current through the LED as it can.  In some cases, this may damage the microcontroller, the LED, or both.

The approach on the left has the LED on when the microcontroller pin is high.  This makes more sense when writing the code but isn't always best.  Sometimes microcontroller pins able to sink (or absorb) more current than they can source (or produce).  If the pin can sink more current, you can make the LED brighter by using the approach on the right.  Just make sure you size your resistor so that the current through the LED and resistor doesn't exceed what the microcontroller pin is capable of sourcing (or sinking).

Wrap Up
----------------

I hope this helps you understand how to better use Ohm's law to design circuits associated with microcontroller inputs and outputs.  These two simple equations--V=IR and P=IV--go a very long way when designing simple microcontroller IO circuits.  They are useful for designing pull-up and pull-down resistor circuits, stepping down voltages as well as driving LEDs.
