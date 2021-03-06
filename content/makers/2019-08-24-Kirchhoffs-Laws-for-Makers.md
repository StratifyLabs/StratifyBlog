---
categories:
- EE for Makers
date: "2019-08-24"
layout: post
tags:
- circuit
- EE for Makers
title: Kirchhoff's Laws for Makers
katex: true
---

> This is the second article in a [series](/tags/EE-for-Makers/) to teach you all the EE you need to know for working with and designing microcontroller based circuits.

In 1845, German physicist Gustav Kirchhoff came up with two equations that generalized the application of [Ohm's law]({{< relref "2019-08-06-Ohms-Law-for-Makers.md" >}}). Kirchhoff's Current Law (KCL) says the sum of all current going into a node must be zero. Kirchhoff's Voltage Law (KVL) states that the sum of all voltages around a closed loop is zero. Let's look at how these are applied to a simple circuit.

## Kirchhoff's Current Law or KCL

KCL says the sum of all currents into a node is zero. So, what is a node? A node is any point on a wire in a schematic. It is more useful if the point has more than 2 places for current to go. Check out node `A` below.

{{< html >}}
<center>
<svg xmlns="http://www.w3.org/2000/svg" style="background-color: rgb(255, 255, 255);" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="417px" height="302px" viewBox="-0.5 -0.5 417 302"><defs/><g><path d="M 288.14 180.14 L 288.14 160.14 L 288 155" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 288.14 280.14 L 288.14 300.14 L 48.14 300.14 L 48.14 160.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 238 230 L 256 230 L 260 220 L 268 240 L 276 220 L 284 240 L 292 220 L 300 240 L 308 220 L 316 240 L 320 230 L 338 230" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,288,230)" pointer-events="none"/><g transform="translate(249.5,223.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="25" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1k<b>Ω</b></div></div></foreignObject><text x="13" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">1k&lt;b&gt;Ω&lt;/b&gt;</text></switch></g><path d="M 48.14 60.14 L 48.14 0.14 L 208.14 0.14 L 208.14 20.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M -2 110 L 43 110 M 53 80 L 53 140 M 53 110 L 98 110" fill="none" stroke="#000000" stroke-miterlimit="10" transform="translate(0,110)scale(1,-1)translate(0,-110)rotate(-270,48,110)" pointer-events="none"/><rect x="39" y="95" width="4" height="30" fill="#000000" stroke="#000000" transform="translate(0,110)scale(1,-1)translate(0,-110)rotate(-270,48,110)" pointer-events="none"/><g transform="translate(-1.5,103.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="16" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">5V</div></div></foreignObject><text x="8" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">5V</text></switch></g><path d="M 208.14 120.14 L 208.14 149.86 L 287 149.86 L 287 146.43 L 284.71 146.43" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 158 70 L 176 70 L 180 60 L 188 80 L 196 60 L 204 80 L 212 60 L 220 80 L 228 60 L 236 80 L 240 70 L 258 70" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,208,70)" pointer-events="none"/><g transform="translate(169.5,63.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="25" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1k<b>Ω</b></div></div></foreignObject><text x="13" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">1k&lt;b&gt;Ω&lt;/b&gt;</text></switch></g><path d="M 369.29 20.14 L 369.29 0.14 L 208.14 0.14 L 208.14 20.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 319 70 L 337 70 L 341 60 L 349 80 L 357 60 L 365 80 L 373 60 L 381 80 L 389 60 L 397 80 L 401 70 L 419 70" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,369,70)" pointer-events="none"/><g transform="translate(330.5,63.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="25" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1k<b>Ω</b></div></div></foreignObject><text x="13" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">1k&lt;b&gt;Ω&lt;/b&gt;</text></switch></g><ellipse cx="288" cy="150" rx="5" ry="5" fill="#000000" stroke="none" pointer-events="none"/><g transform="translate(283.5,129.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="8" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 9px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;">A</div></div></foreignObject><text x="4" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">A</text></switch></g><path d="M 369.29 120.14 L 369.29 149.86 L 293 149.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 168 18.29 L 168 133.63" fill="none" stroke="#00ff00" stroke-miterlimit="10" pointer-events="none"/><path d="M 168 138.88 L 164.5 131.88 L 168 133.63 L 171.5 131.88 Z" fill="#00ff00" stroke="#00ff00" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(153.5,72.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="8" height="14" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;background-color:#ffffff;"><div>i<sub>1</sub></div></div></div></foreignObject><text x="4" y="13" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g><path d="M 408 9.29 L 408 124.63" fill="none" stroke="#00ff00" stroke-miterlimit="10" pointer-events="none"/><path d="M 408 129.88 L 404.5 122.88 L 408 124.63 L 411.5 122.88 Z" fill="#00ff00" stroke="#00ff00" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(393.5,63.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="8" height="14" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;background-color:#ffffff;"><div>i<sub>2</sub></div></div></div></foreignObject><text x="4" y="13" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g><path d="M 318 290 L 318 166.37" fill="none" stroke="#00ff00" stroke-miterlimit="10" pointer-events="none"/><path d="M 318 161.12 L 321.5 168.12 L 318 166.37 L 314.5 168.12 Z" fill="#00ff00" stroke="#00ff00" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(333.5,223.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="8" height="14" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;background-color:#ffffff;"><div>i<sub>3</sub></div></div></div></foreignObject><text x="4" y="13" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g></g></svg></center>
{{< /html >}}

In the circuit above, three wires meet at node `A`. KCL says all three currents will sum to zero.

$$ I_1 + I_1 + I_3 = 0A $$

Notice the arrows indicating the direction of all three currents. Positive current flows in the direction of the arrow. If an arrow is going into the node, it is positive. If it is leaving the node, it is negative. If we flipped the direction of \\(I_3\\), our equation would be:

\\[ I_1 + I_2 - I_3 = 0A \\]

And with a little algebra.

$$ I_1 + I_2 = I_3 $$

> The above equation should be intuitive if you think about the physical flow of electrons.

Now we can use some [Ohm's law]({{< relref "2019-08-06-Ohms-Law-for-Makers.md" >}}) substitutions to actually solve for the current values. We will let the voltage at node `A` be \\(V_A\\).

$$ I_1 = \frac{5V - V_A}{1k \Omega} $$
$$ I_2 = \frac{5V - V_A}{1k \Omega} $$
$$ I_3 = \frac{V_A - 0V}{1k \Omega} $$
$$ \frac{5V - V_N}{1k \Omega} + \frac{5V - V_N}{1k \Omega} = \frac{V_N - 0V}{1k \Omega} $$

With this we have one equation and one unknown variable. So we solve for \\(V_A\\).

$$ V_A = \frac{10V}{3 \cdot 1k \Omega} \cdot 1 $$
$$ V_A = \frac{10V}{3} = 3.3V $$

We can now use [Ohm's law]({{< relref "2019-08-06-Ohms-Law-for-Makers.md" >}}) to get the current values.

$$ I_1 = \frac{5V - 3.3V}{1k \Omega} = 0.0017A = 1.7mA $$
$$ I_2 = \frac{5V - 3.3V}{1k \Omega} 0.0017A = 1.7mA $$
$$ I_3 = \frac{3.3V - 0V}{1k \Omega} 0.0033A = 3.3mA $$

> We have a little rounding error \\(10V / 3 \approx 3.3V \\). If we used \\(V_A = 3.\overline{3}V \\), then we wouldn't have the error. But in most digital circuits, we aren't worried about things like that.

Our original equation is then.

$$ 1.7mA + 1.7mA = 3.3mA $$

KCL is pretty simply but it can be applied to all kinds or circuits. Let's see how KVL works.

## Kirchhoff's Voltage Law or KVL

KVL is basically KCL but with voltages and loops rather than currents and nodes. It states that sum of the voltages around a closed loop in a circuit is zero.

{{< html >}}
<center>
<svg xmlns="http://www.w3.org/2000/svg" style="background-color: rgb(255, 255, 255);" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="438px" height="386px" viewBox="-0.5 -0.5 438 386"><defs/><g><path d="M 307.14 307.14 L 307.14 327.14 L 67.14 327.14 L 67.14 187.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 257 257 L 275 257 L 279 247 L 287 267 L 295 247 L 303 267 L 311 247 L 319 267 L 327 247 L 335 267 L 339 257 L 357 257" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,307,257)" pointer-events="none"/><g transform="translate(268.5,250.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="25" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1k<b>Ω</b></div></div></foreignObject><text x="13" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">1k&lt;b&gt;Ω&lt;/b&gt;</text></switch></g><path d="M 67.14 87.14 L 67.14 27.14 L 227.14 27.14 L 227.14 47.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 17 137 L 62 137 M 72 107 L 72 167 M 72 137 L 117 137" fill="none" stroke="#000000" stroke-miterlimit="10" transform="translate(0,137)scale(1,-1)translate(0,-137)rotate(-270,67,137)" pointer-events="none"/><rect x="58" y="122" width="4" height="30" fill="#000000" stroke="#000000" transform="translate(0,137)scale(1,-1)translate(0,-137)rotate(-270,67,137)" pointer-events="none"/><g transform="translate(17.5,130.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="16" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">5V</div></div></foreignObject><text x="8" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">5V</text></switch></g><path d="M 227.14 147.14 L 227.14 176.86 L 307.14 176.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 177 97 L 195 97 L 199 87 L 207 107 L 215 87 L 223 107 L 231 87 L 239 107 L 247 87 L 255 107 L 259 97 L 277 97" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,227,97)" pointer-events="none"/><g transform="translate(188.5,90.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="25" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1k<b>Ω</b></div></div></foreignObject><text x="13" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">1k&lt;b&gt;Ω&lt;/b&gt;</text></switch></g><path d="M 388.29 47.14 L 388.29 27.14 L 227.14 27.14 L 227.14 47.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 388.29 147.14 L 388.29 176.86 L 307.14 176.86 L 307.14 207.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 338 97 L 356 97 L 360 87 L 368 107 L 376 87 L 384 107 L 392 87 L 400 107 L 408 87 L 416 107 L 420 97 L 438 97" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,388,97)" pointer-events="none"/><g transform="translate(349.5,90.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="25" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1k<b>Ω</b></div></div></foreignObject><text x="13" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">1k&lt;b&gt;Ω&lt;/b&gt;</text></switch></g><path d="M 7.14 112.29 L 7.14 17.14 Q 7.14 7.14 17.14 7.14 L 22.14 7.14 Q 27.14 7.14 37.14 7.14 L 417.14 7.14 Q 427.14 7.14 427.14 17.14 L 427.14 366.86 Q 427.14 376.86 417.14 376.86 L 17.14 376.86 Q 7.14 376.86 7.14 366.86 L 7.14 118.65" fill="none" stroke="#00ff00" stroke-miterlimit="10" pointer-events="none"/><path d="M 7.14 113.4 L 10.64 120.4 L 7.14 118.65 L 3.64 120.4 Z" fill="#00ff00" stroke="#00ff00" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(367.5,350.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="8" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;background-color:#ffffff;">B</div></div></foreignObject><text x="4" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">B</text></switch></g><path d="M 107.14 116.86 L 107.14 57.14 Q 107.14 47.14 117.14 47.14 L 177.14 47.14 Q 187.14 47.14 187.14 57.14 L 187.14 186.86 Q 187.14 196.86 197.14 196.86 L 257.14 196.86 Q 267.14 196.86 267.14 206.86 L 267.14 306.86 Q 267.14 316.86 257.14 316.86 L 117.14 316.86 Q 107.14 316.86 107.14 306.86 L 107.14 123.23" fill="none" stroke="#00ff00" stroke-miterlimit="10" pointer-events="none"/><path d="M 107.14 117.98 L 110.64 124.98 L 107.14 123.23 L 103.64 124.98 Z" fill="#00ff00" stroke="#00ff00" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(142.5,296.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="8" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;background-color:#ffffff;">A</div></div></foreignObject><text x="4" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">A</text></switch></g><g transform="translate(248.5,74.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="14" height="45" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">+<br /><div>V<sub>1</sub></div><div><sub>-<br /></sub></div></div></div></foreignObject><text x="7" y="29" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g><g transform="translate(408.5,69.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="14" height="45" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><div>+<br /></div><div>V<sub>2</sub></div><div><sub>-<br /></sub></div></div></div></foreignObject><text x="7" y="29" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g><g transform="translate(328.5,234.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="14" height="45" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><div>+<br /></div><div>V<sub>3</sub></div><div><sub>-<br /></sub></div></div></div></foreignObject><text x="7" y="29" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g><path d="M 287.14 147.14 L 277.14 147.14 Q 267.14 147.14 267.14 137.14 L 267.14 46.86 Q 267.14 36.86 277.14 36.86 L 337.14 36.86 Q 347.14 36.86 347.14 46.86 L 347.14 137.14 Q 347.14 147.14 337.14 147.14 L 293.51 147.14" fill="none" stroke="#00ff00" stroke-miterlimit="10" pointer-events="none"/><path d="M 288.26 147.14 L 295.26 143.64 L 293.51 147.14 L 295.26 150.64 Z" fill="#00ff00" stroke="#00ff00" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(302.5,80.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="9" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;background-color:#ffffff;">C</div></div></foreignObject><text x="5" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">C</text></switch></g><g transform="translate(18.5,114.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="7" height="43" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><div>+</div><div><br /></div><div><sub>-</sub><br /></div></div></div></foreignObject><text x="4" y="28" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g></g></svg></center>
{{< /html >}}

In the circuit above, we have 3 loops labelled `A`, `B`, and `C`.  We will use the convention where a voltage polarity is positive if it follows the loop direction.  Loop `A` flows from the `-` side of `5V` to `+`, so `5V` is positive. KVL for each loop is:

$$ A: 5V + -V_1 + -V_3 = 0 $$

$$ B: 5V + -V_2 + -V_3 = 0 $$

$$ C: -V_1 + V_2 = 0 $$

Notice \\(V_1\\) in loops `A` and `C`. In these loops, \\(V_1\\) has opposite polarity because of the flow of the loop through \\(V_1\\).

We need some additional information to solve these equations because when we substitute \\(V_2 = V_1\\), we end up with one equation and two unknowns. From KCL above we know that \\(V_A = 3.3V \\). In this case \\(V_A\\) is equivalent to \\(V_3\\).

$$ A: 5V + -V_1 + -3.3V = 0 $$
$$ A: 5V - 3.3V = V_1 = 1.7V $$

$$ V_1 = V_2 = 1.7V $$
$$ V_3 = 3.3V $$

## Voltage Divider Circuit

The voltage divider is a very common circuit. Analyzing a voltage divider is a great way to apply KCL and KVL. The following circuit needs to take a 4.2V battery voltage and scale it down to be read by a 3.3V ADC converter.

{{< html >}}
<center>
<svg xmlns="http://www.w3.org/2000/svg" style="background-color: rgb(255, 255, 255);" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="322px" height="277px" viewBox="-0.5 -0.5 322 277"><defs/><g><path d="M 220 255.24 L 220 275.24 L 60 275.24 L 60 190" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 170 205 L 188 205 L 192 195 L 200 215 L 208 195 L 216 215 L 224 195 L 232 215 L 240 195 L 248 215 L 252 205 L 270 205" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,220,205)" pointer-events="none"/><g transform="translate(190.5,198.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="16" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">R2</div></div></foreignObject><text x="8" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">R2</text></switch></g><path d="M 60 90 L 60 0 L 220 0 L 220 20" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 10 140 L 55 140 M 65 110 L 65 170 M 65 140 L 110 140" fill="none" stroke="#000000" stroke-miterlimit="10" transform="translate(0,140)scale(1,-1)translate(0,-140)rotate(-270,60,140)" pointer-events="none"/><rect x="51" y="125" width="4" height="30" fill="#000000" stroke="#000000" transform="translate(0,140)scale(1,-1)translate(0,-140)rotate(-270,60,140)" pointer-events="none"/><g transform="translate(-1.5,133.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="28" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">4.2V</div></div></foreignObject><text x="14" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">4.2V</text></switch></g><path d="M 220 120 L 220 140 L 220 135.24 L 220 160" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 220 120 L 220 139.05 L 255.24 139.05 L 280 138.57" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 170 70 L 188 70 L 192 60 L 200 80 L 208 60 L 216 80 L 224 60 L 232 80 L 240 60 L 248 80 L 252 70 L 270 70" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,220,70)" pointer-events="none"/><g transform="translate(190.5,63.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="16" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Verdana; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: right;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">R1</div></div></foreignObject><text x="8" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Verdana">R1</text></switch></g><g transform="translate(281.5,132.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="29" height="14" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">V<sub>ADC</sub></div></div></foreignObject><text x="15" y="13" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g></g></svg>
</center>
{{< /html >}}

We use KVL and Ohm's law to calculate the values for R1 and R2 in order for \\(V_{ADC}\\) to be a maximum of 3.3V.

$$ V_{ADC} = V_2 $$

$$ 4.2V - V_1 - V_2 = 0V $$

Now some algebra.

$$ 4.2V - 3.3V = V_1 $$
$$ 0.9V = V_1 $$

Now we know the voltage split from KVL we can use KCL to analyze the current.

$$ I_1 - I_2 = 0 $$
$$ I_1 = I_2 $$

And now Ohm's law.

$$ \frac{0.9V}{R_1} = \frac{3.3V}{R_2} $$
$$ \frac{R_2}{R_1} = \frac{3.3V}{0.9V} $$

For our case, let's say \\(R_2\\) is \\(3.3k \Omega\\). So then \\(R_1\\) would be \\(900 \Omega\\).

The voltage divider distills this analysis into a single equation using the input voltage (4.2V) and the output voltage (3.3V).

$$ \frac{R_2}{R_1} = \frac{V_O}{V_I - V_O} $$

Then solve for \\(V_O\\).

$$ \frac{R_2}{R_1} \cdot (V_I - V_O) = V_O $$
$$ V_I \frac{R_2}{R_1} - V_O \frac{R_2}{R_1} = V_O $$
$$ V_I \frac{R_2}{R_1} = V_O + V_O \frac{R_2}{R_1}$$
$$ V_I \frac{R_2}{R_1} = V_O (1 + \frac{R_2}{R_1})$$
$$ V_I \frac{\frac{R_2}{R_1}}{1 + \frac{R_2}{R_1}} = V_O$$
Finally, we get the voltage divider equation.
$$ V_I \frac{R_2}{R_1 + R_2} = V_O$$

If we plug in the values we found above, we get:

$$ 4.2V \frac{3.3k \Omega}{0.9k \Omega + 3.3k \Omega} = 3.3V $$

This is a good equation to become very familiar with. I use it all the time when developing and debugging firmware.

## What Now?

While studying engineering in college, we had to use KCL and KVL to solve for some complex (and arbitrary) resistive circuits. It wasn't very fun, but I got a good grasp. Today, I use KCL and KVL intuitively without thinking about the equations. And I never use them to analyze complex circuits (there is software for that).

A good exercise is to look at some circuits you have dealt with before and think about how KCL and KVL can be applied. These equations will pop up again and again when analyzing microcontroller based circuits. Especially keep an eye out for voltage divider circuits.



