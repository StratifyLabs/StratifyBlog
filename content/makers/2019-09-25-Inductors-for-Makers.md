---
categories:
- EE for Makers
date: "2019-09-25"
layout: post
draft: false
tags:
- circuit
- EE for Makers
title: Inductors for Makers
katex: true
chart: true
---

> This is the fifth article in a [series](/tags/EE-for-Makers/) to teach you all the EE you need to know for working with and designing microcontroller based circuits.


## Understanding the Basics

An inductor is basically a bizarro world [capacitor]({{< relref "2019-09-17-Capacitors-for-Makers.md" >}}). Where a capacitor uses electrical energy, an inductor uses magnetic energy. And while a capacitor charges-up with voltage, an inductor charges-up using current. The equations governing capacitor voltage apply to inductor current.

Physically an inductor is simply a coil of wire. The coil can be wrapped around a magnetic material in order to increase its inductance (kind of like the electromagnets used in electric motors). When current flows in the coils, magnetic energy builds up. The stored energy can be used to supply current to the circuit which is why one of the most common places you will see inductors used is in DC switching power supply circuits.

> If you like the pressurized water system analogy, you can think of an inductor as ...

### Math and Units

Inductance is measured in henrys which is a ratio how much voltage can be created per ampere over time.

$$ henry = \frac{volt \cdot second }{amp} \space \to \space L = \frac{Q}{V} $$

> We honor various super nerds from the past with inductor notation. The *L* used to represent inductance comes from Heinrich Lenz who discovered the relationship between current and magnetic energy in an inductor. The *henry* is from Joseph Henry who discovered inductance (independent of Michael Faraday).

Like capacitors, Ohm's law doesn't directly apply to inductors because they react rather than simply resist. When a device actively reacts, it has impedance rather than just resistance. Impedance is a combined measure of resistance and reactance. Other than the basic lingo, you don't need to know much more about impedance. Let's rather focus on a few use cases that are seen all-the-time in microcontroller circuits.

### Inductor Behavior in Microcontroller Circuits

Inductors have much simpler behavoir in DC circuits than in AC ones. Thankfully that makes them easy to analyze.

When a DC current is applied to an inductor, it has a transient response and a steady-state response. The transient response describes how the inductor charges up (or discharges if the current is removed) and the steady state describes the behavior after the inductor is charged.

#### Transient Response

In the circuit below, we will look at what happens when \\(5A\\) is first applied and when the inductor reaches steady state.

<center>
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="210px" height="219px" viewBox="-0.5 -0.5 210 219"><defs/><g><path d="M 48.14 96.86 L 48.14 36.86 L 57.86 36.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M -2 147 L 43 147 M 53 117 L 53 177 M 53 147 L 98 147" fill="none" stroke="#000000" stroke-miterlimit="10" transform="translate(0,147)scale(1,-1)translate(0,-147)rotate(-270,48,147)" pointer-events="none"/><rect x="39" y="132" width="4" height="30" fill="#000000" stroke="#000000" transform="translate(0,147)scale(1,-1)translate(0,-147)rotate(-270,48,147)" pointer-events="none"/><g transform="translate(-1.5,140.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="16" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">5V</div></div></foreignObject><text x="8" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">5V</text></switch></g><path d="M 177.86 187.14 L 177.86 216.86 L 48.14 216.86 L 48.14 196.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 128 137 L 173 137 M 173 107 L 173 167 M 183 137 L 228 137 M 193 107 C 179.67 124.78 179.67 149.22 193 167" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,178,137)" pointer-events="none"/><g transform="translate(129.5,136.5)rotate(90,10.5,0)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="21" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1uF</div></div></foreignObject><text x="11" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">1uF</text></switch></g><path d="M 157.86 36.86 L 177.86 36.86 L 177.86 67.14 L 177.86 87.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 58 37 L 76 37 L 80 27 L 88 47 L 96 27 L 104 47 L 112 27 L 120 47 L 128 27 L 136 47 L 140 37 L 158 37" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(107.5,17.5)rotate(-90,0,6)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="25" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1k<b>Ω</b></div></div></foreignObject><text x="13" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">1k&lt;b&gt;Ω&lt;/b&gt;</text></switch></g></g></svg>
</center>

The rate at which the capacitor charges depends on the capacitance \\(1 \mu F\\) and the series resistance between the capacitor and the charging source (\\(1k \Omega\\) in this case). The resistance times the capacitance is the time constant (this will come up later) and defines how long the capacitor takes to charge. The equation is:

$$ V_C = V_S \cdot (1 - e^{\frac{-t}{RC}}) $$

The plot below shows our circuit with the resistor at \\(1k \Omega \\) and \\(2k \Omega \\). Notice the increase resistance slows down the capacitors rate of charging.

```chart
    {
    "type": "line",
        "data": {
            "labels": ["0us", "100us", "200us", "300us", "400us", "500us", "600us", "700us"],
            "datasets": [
            {
                "label": "Capacitor Voltage (R = 1kOhms)",
                "data": [0,
                    3.15498155,
                    4.319181384,
                    4.748775418,
                    4.907297202,
                    4.965792326,
                    4.987377242,
                    4.995342156],
                "backgroundColor":"transparent",
                "borderColor":"orange"
            },
            {
                "label": "Capacitor Voltage (R = 2kOhms)",
                "data": [0,
                    1.962716304,
                    3.15498155,
                    3.879231108,
                    4.319181384,
                    4.586432143,
                    4.748775418,
                    4.847391935],
                "backgroundColor":"transparent",
                "borderColor":"blue"
            }
            ]
        }
    }
```


### Steady State Reponse

The equivalent circuit of an inductor in steady state can be modelled by zero resistance. When the inductor is fully charged, the circuit behaves as though it is a short (at least for our considerations).

<center>
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="545px" height="219px" viewBox="-0.5 -0.5 545 219"><defs/><g><path d="M 48.14 96.86 L 48.14 36.86 L 57.86 36.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M -2 147 L 43 147 M 53 117 L 53 177 M 53 147 L 98 147" fill="none" stroke="#000000" stroke-miterlimit="10" transform="translate(0,147)scale(1,-1)translate(0,-147)rotate(-270,48,147)" pointer-events="none"/><rect x="39" y="132" width="4" height="30" fill="#000000" stroke="#000000" transform="translate(0,147)scale(1,-1)translate(0,-147)rotate(-270,48,147)" pointer-events="none"/><g transform="translate(-1.5,140.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="16" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">5V</div></div></foreignObject><text x="8" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">5V</text></switch></g><path d="M 238 107 L 305.65 107" fill="none" stroke="#00ff00" stroke-width="3" stroke-miterlimit="10" pointer-events="none"/><path d="M 314.65 107 L 305.65 111.5 L 305.65 102.5 Z" fill="#00ff00" stroke="#00ff00" stroke-width="3" stroke-miterlimit="10" pointer-events="none"/><path d="M 177.86 187.14 L 177.86 216.86 L 48.14 216.86 L 48.14 196.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 128 137 L 173 137 M 173 107 L 173 167 M 183 137 L 228 137 M 193 107 C 179.67 124.78 179.67 149.22 193 167" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,178,137)" pointer-events="none"/><g transform="translate(129.5,136.5)rotate(90,10.5,0)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="21" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1uF</div></div></foreignObject><text x="11" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">1uF</text></switch></g><path d="M 157.86 36.86 L 177.86 36.86 L 177.86 67.14 L 177.86 87.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 58 37 L 76 37 L 80 27 L 88 47 L 96 27 L 104 47 L 112 27 L 120 47 L 128 27 L 136 47 L 140 37 L 158 37" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(107.5,17.5)rotate(-90,0,6)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="25" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1k<b>Ω</b></div></div></foreignObject><text x="13" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">1k&lt;b&gt;Ω&lt;/b&gt;</text></switch></g><path d="M 377.86 96.86 L 377.86 36.86 L 388.14 36.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 328 147 L 373 147 M 383 117 L 383 177 M 383 147 L 428 147" fill="none" stroke="#000000" stroke-miterlimit="10" transform="translate(0,147)scale(1,-1)translate(0,-147)rotate(-270,378,147)" pointer-events="none"/><rect x="369" y="132" width="4" height="30" fill="#000000" stroke="#000000" transform="translate(0,147)scale(1,-1)translate(0,-147)rotate(-270,378,147)" pointer-events="none"/><g transform="translate(328.5,140.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="16" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">5V</div></div></foreignObject><text x="8" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">5V</text></switch></g><path d="M 508.14 176.86 L 508.14 216.86 L 377.86 216.86 L 377.86 196.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 488.14 36.86 L 508.14 36.86 L 508.14 87.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 388 37 L 406 37 L 410 27 L 418 47 L 426 27 L 434 47 L 442 27 L 450 47 L 458 27 L 466 47 L 470 37 L 488 37" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(437.5,17.5)rotate(-90,0,6)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="25" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1k<b>Ω</b></div></div></foreignObject><text x="13" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">1k&lt;b&gt;Ω&lt;/b&gt;</text></switch></g><path d="M 458 127 L 476 127 L 480 117 L 488 137 L 496 117 L 504 137 L 512 117 L 520 137 L 528 117 L 536 137 L 540 127 L 558 127" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,508,127)" pointer-events="none"/><g transform="translate(520.5,120.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="23" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><b>∞Ω</b></div></div></foreignObject><text x="12" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">&lt;b&gt;∞Ω&lt;/b&gt;</text></switch></g></g></svg>
</center>


## Inductors in Microcontroller Circuits

Now that you have a basic grasp of what an inductor is and how it behaves, if you learn a few rules of thumb you can understand what most inductors are doing. We will start by looking at inductors in switching power supplies.


### Power Inductors in Switching Supplies

Decoupling capacitors are placed next to the power pins of chips in order to both provide energy to the chip and prevent the chip from injecting noise onto the power supply. Digital chips run on a clock. Every clock cycle there is a brief moment when the internal circuitry changes state. In this moment, there is a tiny current spike.

$$ V_L = L \frac{di}{dt} $$

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

The decoupling capacitor is placed physically close to the `VDD` and `GND` pins of the digital chip. This creates two current loops shown as `A` and `B` below. The decoupling capacitor delivers the energy needed for the current spikes on loop `B`. Loop `A` doesn't have to deal with the spikes and just provides steady current to the capacitor.

<center>
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="560px" height="182px" viewBox="-0.5 -0.5 560 182"><defs/><g><path d="M 60.14 39.86 L 60.14 -0.14 L 169.86 -0.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 10 90 L 55 90 M 65 60 L 65 120 M 65 90 L 110 90" fill="none" stroke="#000000" stroke-miterlimit="10" transform="translate(0,90)scale(1,-1)translate(0,-90)rotate(-270,60,90)" pointer-events="none"/><rect x="51" y="75" width="4" height="30" fill="#000000" stroke="#000000" transform="translate(0,90)scale(1,-1)translate(0,-90)rotate(-270,60,90)" pointer-events="none"/><g transform="translate(-1.5,83.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="28" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">3.3V</div></div></foreignObject><text x="14" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">3.3V</text></switch></g><path d="M 309.86 150.14 L 309.86 179.86 L 60.14 179.86 L 60.14 139.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 260 100 L 305 100 M 305 70 L 305 130 M 315 100 L 360 100 M 325 70 C 311.67 87.78 311.67 112.22 325 130" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,310,100)" pointer-events="none"/><g transform="translate(261.5,99.5)rotate(90,10.5,0)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="21" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1uF</div></div></foreignObject><text x="11" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">1uF</text></switch></g><path d="M 169.86 -0.14 L 309.86 -0.14 L 309.86 50.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 429.86 51.86 L 310 51.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 429.86 115.86 L 385.29 115.86 L 385.29 150.14 L 309.86 150.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><image x="429.5" y="19.5" width="128" height="128" xlink:href="https://cdn3.iconfinder.com/data/icons/electronic-devices-vol-1-1/36/computer_chip_electronic_circuit_solicon_integrated-128.png" preserveAspectRatio="none" pointer-events="none"/><g transform="translate(397.5,33.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="25" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 26px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">VDD</div></div></foreignObject><text x="13" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">VDD</text></switch></g><g transform="translate(396.5,93.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="27" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 28px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">GND</div></div></foreignObject><text x="14" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">GND</text></switch></g><path d="M 100.14 99.86 L 100.14 19 Q 100.14 9 110.14 9 L 239.86 9 Q 249.86 9 249.86 19 L 249.86 160.14 Q 249.86 170.14 239.86 170.14 L 110.14 170.14 Q 100.14 170.14 100.14 160.14 L 100.14 126.23" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 100.14 120.98 L 103.64 127.98 L 100.14 126.23 L 96.64 127.98 Z" fill="#000000" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(115.5,141.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="8" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;background-color:#ffffff;">A</div></div></foreignObject><text x="4" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">A</text></switch></g><path d="M 349.86 79.29 L 349.86 69.29 Q 349.86 59.29 359.86 59.29 L 379.86 59.29 Q 389.86 59.29 389.86 69.29 L 389.86 99 Q 389.86 109 379.86 109 L 359.86 109 Q 349.86 109 349.86 102.18 L 349.86 95.37" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 349.86 90.12 L 353.36 97.12 L 349.86 95.37 L 346.36 97.12 Z" fill="#000000" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(360.5,74.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="8" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;background-color:#ffffff;">B</div></div></foreignObject><text x="4" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">B</text></switch></g></g></svg>
</center>

Generally, you will see \\(0.1 \mu F \\) capacitors place right next to pins. You will also see \\(1.0 \mu F \\) and \\(10.0 \mu F \\) capacitors placed in the vicinity of the part. Loop `B` should be kept as small as possible (because it actually acts as an inductor which counteracts the effects of the capacitor). For designs that are less than 50MHz, these simples rules will usually do the trick. The higher the frequency is, the more challenging it is to design circuit boards with good decoupling capacitors.

Now that you know all that, you can basically just ignore all the decoupling capacitors on a microcontroller schematic. Unless you are having some really weird noise or power issues then they are doing their job just fine.

### Filtering Capacitors

Decoupling capacitors are actually a filtering capacitor for power lines, but capacitors are also used when filtering sensor signals. This is most commonly seen with low pass filters that are used before inputting a signal to an ADC.

The circuit below is a basic low pass filter circuit. Because of how the capacitor charges and discharges slowly, fast signals from \\(V_I\\) will not make it to \\(V_O\\).

<center>
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="401px" height="196px" viewBox="-0.5 -0.5 401 196"><defs/><g><path d="M 269.86 160.14 L 269.86 189.86 L 20.14 189.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 269.86 160.14 L 269.86 189.86 L 380.14 189.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 220 110 L 265 110 M 265 80 L 265 140 M 275 110 L 320 110 M 285 80 C 271.67 97.78 271.67 122.22 285 140" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,270,110)" pointer-events="none"/><g transform="translate(221.5,109.5)rotate(90,10.5,0)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="21" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1uF</div></div></foreignObject><text x="11" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">1uF</text></switch></g><path d="M 181.29 9.86 L 269.86 9.86 L 269.86 60.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 81.29 9.86 L 50.43 9.86 L 20 10" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 181.29 9.86 L 280.71 9.86 L 380 10" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 81 10 L 99 10 L 103 0 L 111 20 L 119 0 L 127 20 L 135 0 L 143 20 L 151 0 L 159 20 L 163 10 L 181 10" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(119.5,27.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="22" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1kΩ</div></div></foreignObject><text x="11" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">1kΩ</text></switch></g><ellipse cx="385" cy="10" rx="5" ry="5" fill="#ffffff" stroke="#000000" pointer-events="none"/><ellipse cx="385" cy="190" rx="5" ry="5" fill="#ffffff" stroke="#000000" pointer-events="none"/><ellipse cx="15" cy="190" rx="5" ry="5" fill="#ffffff" stroke="#000000" pointer-events="none"/><ellipse cx="15" cy="10" rx="5" ry="5" fill="#ffffff" stroke="#000000" pointer-events="none"/><g transform="translate(10.5,77.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="18" height="45" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 19px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;"><div>+</div><div>V<sub>IN</sub></div><div><sub>-</sub><br /></div></div></div></foreignObject><text x="9" y="29" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g><g transform="translate(365.5,67.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="29" height="45" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 30px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;"><div>+</div><div>V<sub>OUT</sub></div><div><sub>-</sub><br /></div></div></div></foreignObject><text x="15" y="29" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g></g></svg>
</center>

The frequency at which half of the power is absorbed in the capacitor is known as the cutoff frequency. For this circuit it is:

$$ \omega _{cutoff} = 2 \pi f = \frac{1}{RC} $$

$$ f_{cutoff} = \frac{1}{2 \pi \cdot 1k\Omega \cdot 1 \mu F} = 159.15Hz $$

So frequencies below \\(159.15Hz\\) can deliver more than half the power than they could without the filter while frequencies above \\(159.15Hz\\) can deliver less than half the power. As the frequency gets higher, less power can be transferred across the filter.

### DC Blocking Capacitors

DC blocking capacitors are used with audio signals to remove the average voltage of the signal so that it is centered around \\(0V\\).

<center>
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="501px" height="81px" viewBox="-0.5 -0.5 501 81"><defs/><g><ellipse cx="435" cy="30" rx="5" ry="5" fill="#ffffff" stroke="#000000" pointer-events="none"/><ellipse cx="65" cy="30" rx="5" ry="5" fill="#ffffff" stroke="#000000" pointer-events="none"/><g transform="translate(10.5,7.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="18" height="45" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 19px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;"><div>+</div><div>V<sub>IN</sub></div><div><sub>-</sub><br /></div></div></div></foreignObject><text x="9" y="29" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g><g transform="translate(465.5,7.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="29" height="45" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 30px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;"><div>+</div><div>V<sub>OUT</sub></div><div><sub>-</sub><br /></div></div></div></foreignObject><text x="15" y="29" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g><path d="M 190 30 L 70 30" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 290 30 L 430 30" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 190 30 L 235 30 M 245 0 L 245 60 M 235 0 L 235 60 M 245 30 L 290 30" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(229.5,67.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="21" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1uF</div></div></foreignObject><text x="11" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">1uF</text></switch></g></g></svg>
</center>

The circuit above just causes an AC signal with a DC offset on \\(V_I\\) to become a zero-centered AC signal on \\(V_O\\). This technique is used a lot with audio signals to zero-center the signal.

```chart
    {
    "type": "line",
        "data": {
            "labels": ["0us", "100us", "200us", "300us", "400us", "500us", "600us", "700us", "800us", "900us"],
            "datasets": [
            {
                "label": "Vin",
                "data": [
                    3,
                    5,
                    3,
                    5,
                    3,
                    5,
                    3,
                    5,
                    3,
                    5
                    ],
                "backgroundColor":"transparent",
                "borderColor":"orange"
            },
            {
                "label": "Vout",
                "data": [
                    -1,
                    1,
                    -1,
                    1,
                    -1,
                    1,
                    -1,
                    1,
                    -1,
                    1
                    ],
                "backgroundColor":"transparent",
                "borderColor":"blue"
            }
            ]
        }
    }
```

This circuit configuration is actually called a high pass filter and basically does the opposite of what the low pass filter does. It prevents signal transfer of low frequencies (you know, like near zero aka DC values).

### You are (hopefully) smarter for having read this

Hopefully, you grasped the basics of how capacitors work (of course, you did). Capacitors are just two metals plates that store a charge. They are basically used for filtering signals. Of course they are used for more advanced circuits, but the vast majority of what I see are power decoupling caps, low pass filter caps and DC blocking (or high pass) caps.