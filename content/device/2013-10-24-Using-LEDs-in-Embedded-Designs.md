---
categories:
- Device Tips
date: "2013-10-24"
layout: post
page_source: PcbBlog
tagline: Embedded Design
tags:
- microcontroller
- circuit
- cpp
- programming
title: Using LEDs in Embedded Designs
katex: true
---

![LED Circuit](/images/LED-circuit.svg)
LEDs are a common element of many embedded designs.  Though the devices are simple; their implementation is not always simplistic.  Optimizing LED cost and performance includes ensuring the driving circuitry is providing the correct voltage and current.

The brightness of an LED is determined by the current that runs through it.  Typical applications use a current limiting resistor in series with an LED to set the brightness at a fixed level.  The following diagram shows a circuit that powers an LED using such a resistor.

To optimize the performance of the LED, the resistor must be sized correctly (note the resistor can be either above or below the LED without affecting the operation or performance of the circuit).  The LED manufacturer provides the rated current and forward voltage values that are essential to calculating the resistor size.  The rated current is the optimal current value for the LED, and the forward voltage is the drop in voltage across the LED when the rated current is applied.  The following diagram shows fictitious but typical values for VCC, the forward voltage, and the rated current.

![LED Circuit Annotated](/images/LED-circuit-annotated.svg)

The value of the resistor is calculated using the given constraints and Ohm's law.  The voltage drop across {{< katex inline >}}R_1{{< /katex >}} is 2.1V, which is VCC (3.3V) minus the forward voltage across the diode (1.2V).  The current through {{< katex inline >}}R_1{{< /katex >}}--the same as the rated current of {{< katex inline >}}R_1{{< /katex >}}--is 20mA.  From Ohm's law, {{< katex inline >}}R_1{{< /katex >}} is {{< katex inline >}}105 \Omega{{< /katex >}}.

{{< katex >}}
V = I \cdot R
{{< /katex >}}
{{< katex >}}
2.1V = 20mA \cdot R_1
{{< /katex >}}
{{< katex >}}
R_1 = \frac{2.1V}{20mA}=105 \Omega
{{< /katex >}}


The rated current, {{< katex inline >}}20mA{{< /katex >}}, is the maximum that can be safely, continuously applied.  Therefore, {{< katex inline >}}105 \Omega {{< /katex >}} is the minimum value for {{< katex inline >}}R_1{{< /katex >}}.  Decreasing the value of {{< katex inline >}}R_1{{< /katex >}} allows more current to flow through D1; increasing {{< katex inline >}}R_1{{< /katex >}} decreases the current through {{< katex inline >}}D_1{{< /katex >}} as well as the brightness.

In this circuit {{< katex inline >}}D_1{{< /katex >}} is always on.  The ground or power connection can be replaced with driving circuitry that is dynamically controlled with an MCU to make the LED blink or dim.

## Driving Circuitry

LED's can be driven using a GPIO pin or--if more current is needed--a transistor.  Depending on the current capabilities of the GPIO pin, it may act as either a sink or a source for the current through the LED.  The datasheet for the MCU will specifiy how much current each pin can sink or source.  The following picture shows an example of the GPIO acting as a current sink.

![LED GPio Sink](/images/LED-GPIO-sink.svg)


When the GPIO outputs a low value, the LED turns off.  Conversely when the GPIO outputs a high value the LED is off.  Interestingly, the LED is also off when the pin is configured as an input.  The GPIO is called a current sink in this case because the current flows in to the pin (much like water flows down the drain of a kitchen sink).  The pin can also act a a current source if configured as shown below.

![LED GPIO Source](/images/LED-GPIO-source.svg)

In this case, the LED is on when the GPIO outputs a logic one.  In both these scenarios, the current through the LED is limited by the sinking/sourcing capabilities of the GPIO.  This limitation is overcome by using a transistor to drive the LED (shown below).

![LED NMOS](/images/LED-NMOS.svg)

To turn the LED on, Q1 needs to be turned on; that is, the GPIO pin outputs a logic one.  Outputting a logic zero on the pin turns the LED off.  In this case, if the GPIO is configured as an input, Q1 will be in an un-stable state, and the light may be on, off, or flickering.  If keeping Q1 in a stable state is critical, a pull-up or pull-down resistor may be added to the gate of Q1 to ensure a known state while the GPIO pin is initializing.

Once the circuit is configured to drive the LED with a GPIO pin, as in any of the above three examples, the brightness can be adjusted dyncamically by rapidly changing the output of the pin.

![PWM Plot](/images/pwm-plot.svg)

## Dynamic Brightness Adjustment

Pulse width modulation (PWM) is a form of digital to analog conversion that can be used to control the brightness of an LED.  PWM is defined by a period and a duty cycle.  The period is the total time; the duty cycle is the percentage of the period that the LED is on.  The following diagram illustrates several PWM duty cycles.

If the period is set to a relatively large value, such as several seconds, the LED will blink.  Much smaller periods (in the milli- or microsecond range) will make the LED appear to be dimmer with lower duty cycle values and brighter with higher ones.

## Conclusion

Using LEDs in embedded designs is relatively simple (and thus a good starting point for beginners).  It is important to use a current limiting resistor in-line with LEDs to prevent damage.  To optimize the performance of the LED, careful sizing of the resistor is necessary.  However, to just get the LED to turn on and off a broad range of resistors will work.  Depending on the current requirements of the LED and the capabilites of the MCU, LEDs may be driven directly using GPIO or, for high current applications, may require the use of a transistor.  Firmware can then be used to simply blink the LED or use PWM to adjust the brightness.
