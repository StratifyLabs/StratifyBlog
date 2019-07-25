---
categories:
- Device Tips
date: "2019-07-25"
layout: post
tags:
- microcontroller
- programming
- circuit
title: My Favorite Serial Bus
---

The three most popular serial buses I come across for communicating with sensors and other chips are UART, SPI and I2C.

## UART (Least Favorite)

The UART is a two line bus that uses one line for transmitting and one line for receiving. It is very common in MCUs as well as a lot of GPS and radio modules.

It is asynchronous so the TX and RX lines work independently and without any clock synchronization. This means there can be data integrity issues at higher speeds. You can overcome these issues by implementing a protocol with redundancy checking and acknowledgements which is a bit of a pain.

The big reason I don't like this protocol is that too much of the burden for data integrity and synchronization is left to the firmware. SPI and I2C have better data integrity built-in at higher speeds.

Nonetheless, UART is my go-to bus for outputting debugging text.

Pros:

- Firmware is easy for low speed connections
- Common in many devices

Cons:

- Low Speed (<100kbps)
- Bit errors degrade data integrity
- Software is not friendly to binary data
- RX to TX connections can cause some confusion

## SPI (Runner Up)

The SPI bus is a four wire bus in a master/slave configuration. The wires are:

- Master Output Slave Input (MOSI)
- Master Input Slave Output (MISO)
- Serial Clock (SCLK)
- Chip Select (CS - one line per slave device)

It is capable of full-duplex operation but in practice is rarely used as such. The signal naming is better than UART because MISO always connects to MISO where on UART the TX needs to connect to RX and sometimes that is confusing.

Pros:

- High Speed (up to 10's of MHz usually)
- Synchronized with very low bit errors
- Clear signal labels and connections (MISO always connects to MISO)

Cons:

- 4 Modes available (one is sufficient)
- 4 Wire bus plus an additional line to add more slaves
- Acknowledgements must be implemented in firmware

## I2C (My Favorite)

I2C is a two-wire, synchronous, half-duplex bus. The wires are:

- Serial Data (SDA)
- Serial Clock (SCL)

Like SPI, the signals are simple to connect but do require pull-up resistors. It can do multiple slaves on the same bus without adding any additional hardware. It isn't as fast as SPI (up to 1Mbps in fast mode plus) but is speedier than most UART implementations.

It also has acknowledgements built in to the protocol so when you write data to a device, you know it was received (neither SPI or UART do that).

Pros:

- Speedier than UART
- 2 Wires
- Built-In Acknowledgements

Cons:

- Firmware complexity (especially for slaves)

## Final Word

The UART, SPI, and I2C are all great for on-board communication (or even board to board). But when you connect you board to a computer, USB is the way to go.
