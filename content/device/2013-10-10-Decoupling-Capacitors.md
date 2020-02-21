---
categories:
- Device Tips
date: "2013-10-10"
layout: post
page_source: PcbBlog
tagline: Embedded Design
tags:
- circuit
- popular
title: Decoupling Capacitors (and Other Power Rules of Thumb)
chart: true
---
![Decoupling Capacitors](/images/decouple-caps.svg)
It is ubiquitous in the digital electronics industry to use small capacitors
next to power pins.  These capacitors stabilize the power delivered to
digital devices and reduce noise.  They are not the only good idea in
embedded systems power supply design.  Ferrite beads further attenuate
noise on the power supply line while diodes prevent reverse voltage and
overvoltage conditions.

Digital circuitry, especially a microcontroller, typically draws high current in response to an event (such as a clock edge) and uses minimal current between events.  The graph below illustrates hypothetical current spikes on the rising edge of a clock signal.

```chart
    {
    "type": "line",
        "data": {
            "labels": ["0ns", "1ns", "2ns", "3ns", "4ns", "5ns", "6ns", "7ns", "8ns", "9ns", "10ns", "11ns", "12ns", "13ns", "14ns", "15ns", "16ns", "17ns", "18ns", "19ns"],
            "datasets": [
            {
                "label": "Clock",
                "data": [
                    0, 0,
                    0, 0,
                    1, 1,
                    1, 1,
                    0, 0,
                    0, 0,
                    1, 1,
                    1, 1,
                    0, 0,
                    0, 0
                    ],
                "backgroundColor":"transparent",
                "borderColor":"orange"
            },
            {
                "label": "Current Consumption",
                "data": [
                    2, 2,
                    2, 2,
                    5, 2,
                    2, 2,
                    2, 2,
                    2, 2,
                    5, 2,
                    2, 2,
                    2, 2,
                    2, 2
                    ],
                "backgroundColor":"transparent",
                "borderColor":"blue"
            }
            ]
        }
    }
```

To prevent the current spikes in the graph above from propagating through the entire board, a decoupling capacitor is placed close to the digital device.  The capacitor does two things:  1) ensures enough current is delivered to the digital device and 2) reduces the noise on the power and ground planes by minimizing the current loop path.

The schematic below shows a decoupling capacitor next to a digital chip.  The loop `B` show the loop where the current spikes travel.  Without the capacitor, the high current loop extents all the way back to the 3V supply and ground on loop `A`.

{{< html >}}
<center>
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="560px" height="182px" viewBox="-0.5 -0.5 560 182"><defs/><g><path d="M 60.14 39.86 L 60.14 -0.14 L 169.86 -0.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 10 90 L 55 90 M 65 60 L 65 120 M 65 90 L 110 90" fill="none" stroke="#000000" stroke-miterlimit="10" transform="translate(0,90)scale(1,-1)translate(0,-90)rotate(-270,60,90)" pointer-events="none"/><rect x="51" y="75" width="4" height="30" fill="#000000" stroke="#000000" transform="translate(0,90)scale(1,-1)translate(0,-90)rotate(-270,60,90)" pointer-events="none"/><g transform="translate(-1.5,83.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="28" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">3.3V</div></div></foreignObject><text x="14" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">3.3V</text></switch></g><path d="M 309.86 150.14 L 309.86 179.86 L 60.14 179.86 L 60.14 139.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 260 100 L 305 100 M 305 70 L 305 130 M 315 100 L 360 100 M 325 70 C 311.67 87.78 311.67 112.22 325 130" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,310,100)" pointer-events="none"/><g transform="translate(261.5,99.5)rotate(90,10.5,0)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="21" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1uF</div></div></foreignObject><text x="11" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">1uF</text></switch></g><path d="M 169.86 -0.14 L 309.86 -0.14 L 309.86 50.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 429.86 51.86 L 310 51.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 429.86 115.86 L 385.29 115.86 L 385.29 150.14 L 309.86 150.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><image x="429.5" y="19.5" width="128" height="128" xlink:href="https://cdn3.iconfinder.com/data/icons/electronic-devices-vol-1-1/36/computer_chip_electronic_circuit_solicon_integrated-128.png" preserveAspectRatio="none" pointer-events="none"/><g transform="translate(397.5,33.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="25" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 26px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">VDD</div></div></foreignObject><text x="13" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">VDD</text></switch></g><g transform="translate(396.5,93.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="27" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 28px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">GND</div></div></foreignObject><text x="14" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">GND</text></switch></g><path d="M 100.14 99.86 L 100.14 19 Q 100.14 9 110.14 9 L 239.86 9 Q 249.86 9 249.86 19 L 249.86 160.14 Q 249.86 170.14 239.86 170.14 L 110.14 170.14 Q 100.14 170.14 100.14 160.14 L 100.14 126.23" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 100.14 120.98 L 103.64 127.98 L 100.14 126.23 L 96.64 127.98 Z" fill="#000000" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(115.5,141.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="8" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;background-color:#ffffff;">A</div></div></foreignObject><text x="4" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">A</text></switch></g><path d="M 349.86 79.29 L 349.86 69.29 Q 349.86 59.29 359.86 59.29 L 379.86 59.29 Q 389.86 59.29 389.86 69.29 L 389.86 99 Q 389.86 109 379.86 109 L 359.86 109 Q 349.86 109 349.86 102.18 L 349.86 95.37" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 349.86 90.12 L 353.36 97.12 L 349.86 95.37 L 346.36 97.12 Z" fill="#000000" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(360.5,74.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="8" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;background-color:#ffffff;">B</div></div></foreignObject><text x="4" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">B</text></switch></g></g></svg>
</center>
{{< /html >}}

> Important. Always place capacitors as close as possible to the power and ground pins of all digital devices.

On the PCB layout, the high current loop should be made as small as practical.  Decoupling capacitors are only effective if the impedance between the capacitor and the device is significantly smaller than the impedance between the capacitor and the power source.  The impedances are directly proportional to the length of the traces.  The impedance mismatch allows the capacitor to quickly discharge when the device needs high current then charge at a slower rate with current delivered from the power supply.

## Ferrite Beads

Ferrite beads can be used to further reduce the amount of noise emitted from digital circuitry on to the power supply planes.  Using ferrite beads is especially important in mixed signal designs where digital noise can disrupt precise analog measurements.  The circuit below illustrates a 1.8V power supply for an ethernet switch.  FB2 is placed between the analog power supply and digital power supply to prevent noise from the digital circuitry from disrupting the analog circuitry.  

![Analog Decoupling](/images/analog-decouple.svg)

Ferrite beads are rated for impedance at frequency (100 Ohm @ 100MHz above).  Ferrite beads essentially act as short circuits at low frequencies and absorb high frequency energy dissipating it as heat.  The frequency rating (100MHz in this example) does not refer to the digital clock frequency.  It refers to the frequency of the high current spike, which is typically in the hundreds of megahertz range for most digital circuitry.

## Diodes (Traditional and Zener)

Diodes are another great power supply design tool.  They can provide protection against reverse voltage (traditional diodes) and over voltage (zener diodes).  Using traditional diodes in a half bridge configuration (as in the image below) provides reverse voltage protection.

![Half Bridge](/images/half-bridge.svg)

The drawback of using a half bridge is that the diodes dissipate some amount of energy as heat reducing the efficiency of the system overall.  

A full bridge diode configuration is a clever solution which allows the circuit to work regardless of polarity albeit with the same efficiency reduction as the half bridge.  Full bridge rectifiers are typically used to convert an AC signal to DC.  However, when used with a DC power supply, the power supply is converted to a positive voltage given a negative or positive input.  This concept is illustrated in the following diagrams with the red arrows showing the respective current paths.  Notice that the current through the resistive load is in the same direction in both illustrations.

![Full Bridge A](/images/full-bridge-a.svg)

![Full Bridge B](/images/full-bridge-b.svg)


While the above bridges use traditional diodes to protect against reverse polarity, zener diodes protect against over-voltage and are selected based on the reverse breakdown voltage.  For example, for a circuit that operates at 3V but has an absolute maximum rating of 6V, a zener diode with a reverse breakdown voltage of 5V protects the circuit against over-voltage.  Even if there is no chance the user will apply an overvoltage, zener diodes also protect against voltage surges due to EMI or electrostatic discharge.  The image below shows a resistive load with a half bridge circuit and zener diode protections.

![Zener Protection](/images/zener-protection.svg)

## Conclusion

Decoupling capacitors, ferrite beads, and diodes are vital components to embedded systems power supplies.  Decoupling capacitors ensure steady power is delivered to digital chips and help reduce noise on the power supply lines.  Ferrite beads act to further reduce noise while diodes offer both reverse polarity and over voltage protection.
