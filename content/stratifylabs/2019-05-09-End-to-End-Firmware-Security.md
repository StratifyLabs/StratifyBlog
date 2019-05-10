---
categories:
- Product Management
date: "2019-05-09"
layout: post
tags: [business, product development, stratifylabs]
title: End to End Firmware Security
---

One of the biggest concerns we get from customers is keeping their firmware secure. This concern is especially true when manufacturing products overseas where there is less visibility and oversight in the process. Stratify Labs has you covered when it comes to end-to-end firmware security. The best part is that the entire process is seamlessly integrated into the Stratify OS ecosystem and can be done without special crytographic hardware.

## Achieving End-to-End Firmware Security

When we say "end-to-end", we are talking about the first time the firmware is ever loaded on a new unit to over-the-internet updates performed either automatically or by customers or service technicians. Keeping firmware secure means never allowing unencrypted copies to be transmitted over any medium where someone could hack in and spy.

### The Hardware Setup

The hardware setup includes a linux single board computer (usually a raspberry pi) that can be deployed anywhere in the world and completely automates the entire process. The single board computer connects to new product using both a SWD/JTAG connection and a secondary connection that is used for field updates. The secondary connection can be USB, bluetooth, ethernet, wifi or a low-level interface (UART/I2C/SPI). The single board computer connects to the internet using ethernet or WIFI.

> **SWD/JTAG** If you are not familiar with SWD/JTAG, it is an industry standard protocol that can be used to load firmware onto just about any microcontroller.

### Securing the Flash Loader

The first vulernable point is the JTAG/SWD connection. The factory loading the firmware could spy on these lines and see exactly what data is being transferred. So if the firmware image is transferred unsecurely, it can be stolen. To overcome this, we use public/private key encryption.

> The flash loader is the code that gets installed in RAM and is used to install the bootloader. The flash loader code itself is generic while the bootloader is proprietary. 

The single board computer generates the public/private key internally. The private key is kept secret (never sent anywhere) while the public key is sent to the target over SWD/JTAG along with a flash loader that is capable of using the public key to establish secure communication with the single board computer. Once the secure connection is established, a secure bootloader using a secret key that is unique to the serial number of the target is securely flashed on the device and the JTAG/SWD is disabled.

The single board computer gets authorization from the Stratify Cloud to install a copy of the secure bootloader. Our customers control the exact number of target devices that are authorized. Once the specified number of target devices have been programmed, the single board computer will cease to program new boards until authorized through the Stratify Cloud.

Finally, every installed copy is registered in the Stratify Cloud. Our customers are able to see exactly when each copy was installed as well as the serial number for each device.

> **As a Bonus** The single board computer is also capable of installing and executing test applications. The results of which are also stored in the Stratify Cloud with the associated serial number.

### Securing the Bootloader

The bootloader (installed by the flash loader) manages the startup of the device. When it starts, it checks the system for a valid instance of Stratify OS to start executing. If one does not exist, it listens for an update externally using USB/ethernet/wifi/bluetooth/etc.

Each device is programmed with a unique secret key that is stored in both the bootloader and the Stratify Cloud. When it is time to update the firmware, the Stratify Cloud serves a uniquely encrypted copy of the firmware that is downloaded to the device either using the bootloader directly (ethernet/wifi) or using [sl](https://docs.stratifylabs.co/sl-manual/) over USB. Once the image is downloaded, the bootloader decrpyts it and loads it into internal flash. When the device is updated, its status in the Stratify Cloud is updated.

Additionally, [Stratify OS applications](https://docs.stratifylabs.co/Guide-Stratify-OS/) are installed using the same process. Applications provide for a much faster way to incrementally update firmware (in many cases the firmware can be updated without even resetting the device).

### Securing User Data

In addition to upgrading firmware, the Stratify Cloud also allow users to log and monitor any data stream. The data is secured using the same secret key that is part of the bootloader. The Stratify Cloud uses this key to provide fast, secure, low-overhead data transmission instead of using TLS which requires a expensive handshake (usually between 15 and 60 seconds) and tens of kilobytes of RAM per connection.

## That's It

If you are ready to go end-to-end secure (in the most seamless way possible), you should [request a demo](https://stratifylabs.co) so we can show you exactly how easy it is.
