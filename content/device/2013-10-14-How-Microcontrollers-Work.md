---
categories:
- Device Tips
date: "2013-10-14"
layout: post
page_source: MicrocontrollerBlog
tagline: Embedded Design
tags:
- microcontroller
- circuit
title: How Microcontrollers Work
---

![Silicon Atom](/images/sili-atom.svg)
Microcontrollers are small computers that contain a central processor, memory, and input/output circuitry all on one integrated chip. Computers in general contain all these components with varying degrees of integration. Most people are familiar with the CPU acronym which refers to the central processing unit. The core of a microcontroller is also a central processing unit and is also the focus of this article.

Microcontroller processors are built on a silicon base. The silicon has extra elements added in a controlled manner to create millions of semiconductor devices in a tiny area. The devices are arranged and connected--basically with tiny integrated wires--to form logic gates that use boolean algebra to perform mathematical operations (like addition and subtraction). This type of logic is called combinational logic. The semiconductor devices are also arranged to form memory (such as RAM) using sequential logic. These logical building blocks are used by microcontroller designers to create the various parts of a microcontroller's CPU such as the arithmetic logic unit (ALU), CPU registers, buses, and memory.

From the atomic level to the CPU architecture, here is an overview of how microcontrollers work:

1. Semiconductor Physics: silicon is doped to create semiconducting devices
1. Transistors: semiconductor material is arranged to form tiny on/off switches
1. CMOS: complementary transistors come together to form circuits that perform boolean algebra
1. Logic: both combinational and sequential logic is used to form the major building blocks of a CPU
1. Microcontroller Architecture: digital logic circuitry comes together to execute instructions

## Semiconductor Physics

Modern semiconductors are made primarily from silicon. Silicon, per se, does not conduct electricity; however when impurities are introduced, silicon semi-conducts electricity. To understand how silicon is transformed from a non-conductor to a semi-conductor, it is important to be familiar with the Lewis octet rule.

The Lewis octet rule is a rule of thumb in chemistry that says atoms with a low number of electrons (low atomic number) tend to bond with other atoms in such a way to produce eight valence electrons. A valence electron is one in the outer regions and is used to bond with other atoms. A single silicon atom has four valence electrons (as shown in the image at the top of the page).

When silicon atoms are around other silicon atoms, they share valence electrons such that each has eight--thus fulfilling the Lewis octet rule. The following diagram illustrates this in two dimensions. Of course in real life, the bonding happens in three dimensions.

![Silicon Atom Group](/images/sili-atom-group.svg)

> Note. This model inspired the [Stratify OS logo](https://stratifylabs.co/).  The symbol inside the gear is a silicon atom.

> Note. The center atom has four valence electrons of its own and shares one from each of four surrounding atoms.

With pure silicon, the Lewis octet rule holds up nicely. When impurities are introduced, instead of four valence electrons, there might be only three valence electrons on some atoms; this is the case when Boron is added. Other impurities, such as nitrogen add an electron.

> Note. When a material has extra valence electrons, it is more negative (N-type).

> A material that has fewer valence electrons is more positive and (P-type).

With extra electrons bouncing around, the current can flow through the material when a potential is applied. In the diagram below, new electrons are injected from the "-" site while being attracted to the "+" site. The extra electrons provided by
the nitrogen atoms allow the electrons to flow across the material.

![Silicon nit group](/images/sili-nit-group.svg)

Silicon can also be doped with Boron. Instead of having extra electrons, it has "holes" (less than eight valence electrons). These holes provide a place for electrons to jump across when a voltage is applied. The resulting material (Silicon doped with Boron) also conducts current.

Thus far, the materials mentioned are still conductors rather than semiconductors. It is only when the above materials are combined do they become semiconductors.

## Diodes, the PN Junction

The diode is the most basic semiconductor device. It permits the current to flow in one direction and prevents it from flowing in the opposite direction. It essentially acts as a one-way valve. A diode is created by juxtaposing a P-type material and an N-type material (known as the PN junction). The diagram below
shows a PN junction with N-type doping on the left using Nitrogen and P-type doping on the right using Boron. The red electrons represent the extra valence electrons provided by the Nitrogen atoms, and the empty electrons represent the "holes" introduced by the Boron atoms.

![PN Junction](/images/p-n-junction.svg)

When a positive voltage is applied to the P-type side of a PN junction, current flows. The extra electrons on the N-type side are pulled over to the P-type side while the negative potential injects electrons.

> Note. Current flows from positive to negative, but electrons flow from negative to positive because electrons have a negative charge value.

The diagram below illustrates how electrons are pulled across a PN junction.

![PN Junction Flow](/images/pn-junction-flow.svg)

When the voltage potential is reversed, the current does not flow across the junction. All the electrons on the N-type side are pulled to the positive potential, and the electrons injected in the P-type side fill the existing holes. However, the electrons that occupy the holes are unable to flow across the N-type side because the positive potential has effectively removed the doping leaving the equivalent of pure, non-conducting Silicon.

![PN Junction No Flow](/images/pn-junction-noflow.svg)

The diode perfectly illustrates the properties of semiconductors, but, it is not the main building block of microcontrollers. Transistors are.

## Field Effect Transistors (FETs)

The first widely-used transistors were the bi-polar junction transistors (BJTs). They are created by using an additional doping material with the PN junction resulting in either an NPN or PNP transistor. However, BJTs are better at amplification than switching. The second widely used transistors were the Field effect transistors (FETs). FETs are typically "on" or "off" and rarely operate in the linear region between these states making them well-suited as tiny switches.

FETs also use N-type and P-type materials but the arrangement is more complex and diodes or BJTs. The metal-oxide-semiconductor FET (MOSFET) was the first FET and one of the most common in use today. The diagram below shows the anatomy of an N-channel MOSFET (or NMOS).

![NMOS Transistor](/images/nmos.svg)

The NMOS consists of a P-type substrate with two N-type regions connected to the source and drain. The gate is connected to the substrate through an insulating oxide layer (electrically it acts as a capacitor). The gate is used to control whether the switch is on or off. When the gate-source voltage is 0V, the substrate acts like two diodes that are butted together in opposite directions preventing current flow in both directions. When the gate-source voltage is above a threshold, the positive charge on the gate pushes away the positively charged "holes" in the P-type region and attracts negatively charged electrons creating a depletion layer that is effectively a channel of N-type material (hence the name N-channel).

A P-channel MOSFET (PMOS) has the same structure but uses an N-type material for the substrate and a P-type for the drain and source connections. Also, the gate-source voltage must be below a certain (negative) threshold to turn on.

In practice, the source of an NMOS is almost always connected to ground while the source of a PMOS is mostly connected to VDD. This is especially true in single-supply systems. This configuration makes it easy to apply a positive voltage to the NMOS gate and a negative voltage to the PMOS gate (both voltages being with respect to the source). The diagram below shows simplified schematic symbols for NMOS and PMOS transistors. Though the symbols have horizontal symmetry, the conventional wisdom mentioned above holds for subsequent illustrations meaning NMOS source is down (ground) and PMOS source is up (VDD).

![MOSFET Symbols](/images/mosfet-symbols.svg)

NMOS and PMOS transistors work together to form what is called CMOS logic. The C is for "complementary".

## CMOS

To grasp the importance of CMOS logic, understanding boolean algebra is crucial.

## Boolean Algebra

Boolean algebra is named after George Boole who published a book called __An Investigation of the Laws of Thought__ in 1854. In this book, he defined an algebraic system based on the values of 0 and 1. The operations defined are distinct to decimal number operations such as plus and minus as well as multiply and divide. Nonetheless, these boolean operations are the building blocks for implementing traditional algebraic operations using CMOS circuitry.

The basic Boolean operators are NOT, AND, and OR. While AND as well as OR have two or more inputs, NOT operates on a single input value. Boolean operations are typically expressed using a truth table (a table of inputs and outputs). Here is the truth table for x NOT equals z.

x | z|
--|--|
0 |1 |
1 |0 |


The following truth table expresses x AND y is z.

x | y| z|
--|--|--|
0 |0 |0 |
0 |1 |0 |
1 |0 |0 |
1 |1 |1 |

This table is x OR y equals z.

x | y| z|
--|--|--|
0 |0 |0 |
0 |1 |1 |
1 |0 |1 |
1 |1 |1 |

These operations can also be combined. NOR is a combination of NOT and OR where NOT is applied to the output of OR. Additionally, NAND is NOT applied to the output of AND. These combinations are important for building CMOS circuitry of these operations.

## CMOS Inverter

The diagram below shows a CMOS inverter that performs a boolean NOT--the circuit is also known as a NOT gate. The transistor on the VDD side is a PMOS while the one connected to ground is an NMOS. The drains of the PMOS and NMOS are connected at the voltage output. The gates are also connected at the voltage input.

![CMOS Inverter](/images/cmos-inverter.svg)

When the voltage input is zero volts, the output voltage is VDD (henceforth called logic zero and logic one respectively). With logic zero input, the gate-source voltage of the PMOS is -VDD. This causes the PMOS to conduct current. Effectively, the output is shorted to VDD. In this state, the gate-source voltage on the NMOS is zero volts keeping the NMOS off.

Conversely, when the voltage input is logic one, the output is logic zero. In this state, the NMOS is on, and the PMOS is off. Each input scenario is shown in the diagram below.

![CMOS Inverter on/off](/images/cmos-inverter-on-off.svg)

By adding two more complementary transistors, other gates are created.

## AND Gate

An AND gate is built using a NAND gate and an inverter. The NAND gate is shown in the following diagram.

![CMOS NAND Gate](/images/cmos-nand.svg)

The NAND gate above is built with two NMOS and two PMOS transistors. The transistors are arranged in a complementary--there's the C-word again--configuration with the PMOSs in parallel and the NMOSs in series. To round out the AND gate, the output of the NAND gate is put through a NOT gate (CMOS inverter) to yield the AND truth table.

## OR

OR is implemented similarly to AND. First, a NOR gate's (shown below) output is inverted (shown above). The NOR gate uses two NMOS transistors in parallel with two PMOS transistors in series.

![CMOS NOR Gate](/images/cmos-nor.svg)

With just these three gates, many other logic gates, as well as more advanced digital circuitry, can be built.

## Logic

There are two fundamental types of logic used in microcontrollers: combinational and sequential. The output of combinational logic depends only on the current inputs much like the AND, OR, and NOT gates previously discussed. Sequential logic (the basis for memory circuits) depends both on the present inputs as well as the previous inputs.

## Combinational Logic

Microcontrollers process data by performing mathematical operations. These operations are defined by the instruction set and are performed using circuitry specially designed to add, subtract, multiply, et cetera. The digital circuitry used in these operations is built using logic gates such as AND, OR, and NOT discussed previously in the CMOS section as well as additional gates (which can be built using these gates) including NAND, NOR, XOR, and XNOR.

![Logic Gate Legend](/images/logic-gate-legend.svg)

## Adder Circuit

A binary adder has two inputs and two outputs. The inputs are the bits being added. The outputs are the sum and the carry value. Adding two binary numbers can yield four results: 0 and 1 with and without a carry. The carry bit is added to the next digit of the binary number meaning that the next addition will include three inputs: the bits to be added and the carry from the previous bit.

carry (in)| a| b| carry (out)|sum|
--|--|--|--|--|
0 |0 |0 |0 |0 |
0 |0 |1 |0 |1 |
0 |1 |0 |0 |1 |
0 |1 |1 |1 |0 |
1 |0 |0 |0 |1 |
1 |0 |1 |1 |0 |
1 |1 |0 |1 |0 |
1 |1 |1 |1 |1 |

The top half of the above truth table (where the carry is zero) is called a half adder circuit. The sum is `a XOR b` and the carry is `a AND b`. The entire table (known as a full adder) is represented by the equations below.

* s = (a XOR b) XOR cin
* cout = (a AND b) OR ((a XOR b) AND cin)

These equations are calculated using the following circuit.

![Adder](/images/adder.svg)


The above circuit adds just one bit to another bit. Most microcontrollers have 8, 16, or 32-bit architectures, which means they respectively add 8, 16, or 32 bits at one time. This is done by cascading the adder circuit above. A four-bit adder looks like the following circuit.

![Four bit adder](/images/four-bit-adder.svg)

The circuit above (expanded to 8, 16, or 32 bits) is used when the microcontroller executes an "add" instruction. Every microcontroller has an instruction set that defines which circuitry is available. Most can add, subtract, invert, shift, rotate, and complement. Some can multiply and divide. All the operations are performed using sequential logic gates using the design approach illustrated for the adder circuit starting with CMOS transistors to form logic gates. Before an operation (such as addition) starts, it needs to be loaded from memory. Likewise, once an operation completes, the result is stored in memory. Combinational logic cannot store information but sequential logic can.

## Sequential Logic

While combination logic provides a given output for a given input, sequential logic takes both current and past input values into account when generating an output. The most basic sequential logic circuit is the SR-latch. It stores one bit of data. The circuit below shows how two NOR gates can be used to form an SR latch.

![SR Flip Flop](/images/sr-flip-flop.svg)

Q is the output of the circuit. R is used to reset the output (write to zero), and S is used to set the output (write to one). Q' (pronounced Q not) is always the opposite value of Q. To understand the circuit, consider the truth table for the NOR gate which is the OR output passed through a NOT gate.

a | b|NOR|
--|--|--|
0 |0 |1 |
0 |1 |0 |
1 |0 |0 |
1 |1 |0 |

Now, here are the truth tables of the two NOR gates side by side.

R | Q' (present)| Q (next) | Q (present)| S | Q' (next) |
--|--|--|--|--|--|
0 |0 |1 |0 |0 |1 |
0 |1 |0 |0 |1 |0 |
1 |0 |0 |1 |0 |0 |
1 |1 |0 |1 |1 |0 |

In the above table, notice when R is one, Q is forced to zero and when S is one, Q' is forced to zero, and Q is forced to one. If both S and R are one, the circuit will become unstable and have an undefined output.

S | R|Action|
--|--|--|
0 |0 |Hold |
0 |1 |Reset |
1 |0 |Set |
1 |1 |Undefined |


The SR latch is just one of many types of memory storage circuitry. Static RAM (SRAM) (used in most microcontrollers) is not made up of SR latches but uses the same design principles. Dynamic RAM (DRAM) (used in most desktop computers) combines logic with a capacitor which stores the memory as a charge. Flash memory uses principles similar to SRAM. However, the transistor semiconductor design is tweaked to allow the gates to be programmed to retain their state when power is removed.

Combination and sequential logic circuitry lay the foundation for creating the digital circuitry that makes up a microcontroller's central processing unit.

## Microcontroller Architecture

Microcontroller architecture relies on digital devices created using combinational and sequential logic. When a microcontroller starts up, it goes to a predetermined location in memory and starts executing instructions that are stored there. Typical operations include loading data from memory, executing calculations, and storing data in memory. Some memory regions are designed to not just store data but control the input/output mechanisms of the microcontroller. This memory is typically referred to as peripheral registers because reading and writing the registers control peripheral circuitry (as opposed to the central processing).

A basic microcontroller architecture includes memory, a CPU, and peripheral circuitry. The memory might be flash (typically used for program instructions) and RAM. The CPU contains circuitry that can load and store data from memories as well as perform various calculations such as adding and subtracting using the arithmetic logic unit (ALU). The CPU also has a set of registers, which is a special type of RAM that is directly wired to the ALU. This means the CPU cannot directly add two values from RAM. It must first load the values to the CPU registers to perform the calculation. The general-purpose CPU registers are typically called r0, r1, r2, and so on. Special CPU registers also include the program counter and stack pointer. Finally, microcontrollers contain peripheral circuitry that is controlled using peripheral registers. The peripheral registers are also RAM-like. But instead of the data just sitting there, it actively controls the peripheral circuitry such as the UART, SPI, I2C, GPIO, and so on. Therefore by reading and writing the peripheral registers, the CPU can interact with external devices (such as a Bluetooth radio).

![MCU Architecture](/images/mcu-arch.svg)

For a microcontroller to execute an instruction, it goes through three basic steps.

* Fetch
* Decode
* Execute

When an instruction is fetched, the CPU grabs an instruction from the flash memory using the instruction bus (see 1 above). The CPU uses a special register called the program counter (part of CPU registers above) to keep track of what instruction should be loaded next. The program counter automatically increments--meaning it has special digital circuitry designed using logic to make it increment--each time an instruction is executed. Microcontrollers use branching instructions to make the program counter jump around in the case of executing a loop or an "if" statement.

Once the instruction is fetched, the CPU executes it. For load instructions, this means loading data from RAM or the peripheral registers to the CPU registers using the data bus (see 2 above). Sometimes with load instructions that use indirect addressing, the ALU is used to calculate the address to load. Store instructions work similarly except that data from the CPU registers are stored in RAM or peripheral memory. Math-based instructions pass one or two registers (see 3 above) to the ALU which then performs a calculation (such as addition) and stores the result in a CPU register (see 4 above).

The above-described architecture is not all-encompassing. It is more specifically a Harvard architecture with a load-store instruction set. Many other architectures exist each with various advantages and disadvantages.

## Conclusion

Understanding how microcontrollers work starts with a basic understanding of semiconductor physics. Silicon doped into N-type and P-type materials enable the creation of semiconductor devices such as diodes and transistors. Microcontrollers consist of millions of transistors arranged on a single silicon-based circuit to create many logic gates--such as AND, NOT, OR, XOR--to form the digital circuitry that makes up microcontrollers.
