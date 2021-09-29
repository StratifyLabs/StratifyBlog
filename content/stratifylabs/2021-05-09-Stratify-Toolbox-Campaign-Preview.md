---
categories:
- Stratify Labs
date: "2021-05-09"
layout: post
tags: [business, product development, stratifylabs, toolbox]
title: Stratify Toolbox Campaign Preview
---

> Version 3

I removed the features highlighting instrumentation. I added a focus on the ability to do secure production programming.

> Version 2

This is a preview of the upcoming [Stratify Toolbox Crowd Supply Campaign](https://www.crowdsupply.com/stratify/toolbox).

Your [feedback](https://www.crowdsupply.com/stratify/toolbox/ask-question) is welcome!

> VIDEO PREVIEW COMING SOON

## Rewards

- Stratify Toolbox: 399USD
- Includes 10-Pin ARM SWD Breakout board

> Rewards will show in the right panel

The Stratify Toolbox takes a new approach to **flashing and debugging microcontrollers**.

- **NO host software to install!** It works on ANY device with a browser and/or a command-line
- **Super-easy** wifi setup using the touch display
- **Designed for `printf()`-ers**
- **Remote Access** with no monthly fees or router configuration

![Overview Graphic](/images/toolbox-target-setup.png)


**Fixing `printf()`**

Rather than focusing on breakpoints and code-stepping like most tools, the Stratify Toolbox is built for those who prefer `printf()`-style serial tracing.

![Fixing Printf](/images/fixing-printf.png)

## Focus on Ease-of-Use

**No Software to Install.** There is NO HOST SOFTWARE to install. Last time, there is **no software to install**.

![Fixing Printf](/images/touch-type-click.png)

**3 Ways to Toolbox:**

- Use the **Touch** display for stand-alone operation
- **Type** `curl` commands in the terminal to integrate with your workflow
- **Click** to use the embedded web application as a GUI for flashing and debugging


{{< youtube ol9aOj6xnAg >}}
**Connect to Wifi**

{{< youtube b9TZABBLgmo >}}
**Connect to the Target**

### Remote Access

You can remotely access your Toolbox from anywhere in the world without any router/vpn setup and **no monthly fees**! Do it in three easy steps:

1. Create a free Google Firebase project
1. `curl -X PUT` your Firebase project details on your Toolbox
1. Post binary images to Firebase and download the trace output using `firebase-tools`

When you have your Toolbox setup with remote access, you can easily integrate with your continuous integration tools to run tests after every code commit.


### Production Programming and Test

The Toolbox can be used in a production programming environment to securely program MCU's using ellipitial curve based shared secret between the Toolbox and the target and AES-128 encryption to transfer the binary. The results of all programming and testing can be easily reported to a self-managed Firebase project.

The Toolbox comes standard with support for securely programming STM32 processors (the STM32 needs to meet minimum RAM requirements). Your implementation might require some tweaks to the standard approach in order to improve security. Support for additional processors requires writing a flash driver using a template project.

### What MCUs are Supported?

**Flashing**

All STM32 Parts that can be programming using STM32CubeProgrammer can be remotely flashed using the Toolbox. 

You can create your own binary blob and configuration file to support flashing any ARM Cortex-M chip. You just have to write the flash driver. For most MCU's, the manufacturer provides a flash driver that just needs to be ported to the target loader template project.

**Tracing**

Tracing is supported on SWO, UART, and SPI serial protocols independent of the MCU.

### SDK

The SDK allows you to install your own applications that run on the Toolbox.

- GUI Application: build a customized test or flash programming application for production
- web application: create your own embedded web application. Use the fully open-source `toolbox-web-app` as a starting point
- flash/trace delegate: add support for a UART or I2C serial bootloader protocol

The SDK includes high-level C++ APIs for:

- JSON Handling
- HTTP and HTTPS (using mbedtls) Client
- HTTP Server
- UART/SPI/I2C Drivers
- Analog Input (x2) and Output (x1) Drivers
- SWD/JTAG Drivers
- ELF File Parsing
- Graphics (based on lvgl)
- FAT Filesystem (SD Card)
- Cryptographic Suite
  - ECDSA: Sign code and verify signatures
  - ECDH Shared Secret: Establish a secure session with any target
  - AES 128/256: Fast Symmetrical Encryption
  - SHA256: create unique hashes for binaries and messages

All of the Stratify Toolbox applications are open-source and freely available to modify, improve, and share.

## More Features

The Toolbox includes additional features to help with board bring-up and other firmware programming challenges.

- I2C Scan Tool
- UART Data Logger
- I2C EEPROM Programmer
- Built-in Voltage Reference
- Free software updates
- And more: be sure to sign up for updates for more details!

## Specifications

- 320x240 IPS LCD Capacitive Touch Display
- 1200mAh battery
- 480MHz ARM Cortex M7 processor (STM32H7)
- SWD/JTAG/SPI/UART Side port
- UART/SPI/I2S/Analog Front Expansion port
- Wifi b/g/n (ATWINC1500 or ATWINC3400)
- USB 2.0 Micro B (for charging)
- 1.8V to 5.5V IO Operating Range

## Comparisons

Flash Programmers/Debuggers:

|                                 | Toolbox               | JLink Wifi     | Cyclone         |
|---------------------------------|-----------------------|----------------|-----------------|
| **Manufacturer**                | Stratify Labs, Inc    | Segger         | NXP             |
| **Price**                       | $399 ($499 retail)    | About $998     | About $610      |
| **Max. target interface speed** | 6 MHz                 | 15 MHz         | Unknown         |
| **Stand-alone operation**       | Yes                   | No             | Yes             |
| **PC software required**        | No                    | Yes            | Yes             |
| **SWD/JTAG**                    | Yes                   | Yes            | Yes             |
| **SWO**                         | 6 MHz                 | 30 MHz         | Yes             |
| **Supported voltage range**     | 1.8 V to 5.5 V        | 1.2 V to 5.5 V | 1.2 V / 5 V     |
| **Download speed into RAM**     | 0.8 MB/sec            | 1 MB/sec       | Unknown         |
| **SD card**                     | 256MB (card included) | No             | With upgrades   |
| **Wi-Fi**                       | Yes                   | Yes            | No              |
| **Display**                     | IPS LCD w/ cap. touch | No             | LCD Touchscreen |
| **Customizable Web interface**  | Yes                   | No             | No              |
| **SDK**                         | Yes                   | No             | No              |
| **`printf()`-ing Awesome!**     | Yes                   | No             | No              |

## Support and Documentation

All the documenation you need to operate the Toolbox is served from the web application:

- Quick Start
- HTTP API Reference
- Instrumentation Specification

![Documentation image](/images/docs-mosaic.png)

## Manufacturing Plan, Fulfillment and Logistics

Due to the worldwide shortage of parts, the manufacturing plan is trickier than usual. Normally, I would just outsource to a turn-key PCB assembly house, but with the shortages, that would take a year. The plan is (best case scenario):

- Order crucial parts or alternates (first week)
- Order plastic injection molds and parts (first week)
- Order other mechanical parts such as screws, CE/cert stickers (first week)
- Update and test the design with the alternate parts (8 weeks)
- Test and cerfity the updated design (8 weeks)
- Order commodity parts and PCB's (3 weeks)
- Final assembly with domestic PCB assembly house (8 weeks)
- Send product to Crowd Supply for fulfillment (last week)

Using this plan, the Toolboxes will ship in Q1 of 2022. The worst case scenario is that no alternate parts will be available for crucial components. In which case, the delivery date is pushed back to June or July of 2022.

## Risks and Challenges

The biggest challenge is procuring parts including alternates/substitues and integrating the alternates into the hardware/firmware. The crucial parts are the MCU and the Wifi. The MCU can be a number of different STM32H7 chips and still get the job done. Switching means updating the system firmware to work with the alternate chip which will a 3 to 4 weeks of development time. The Wifi is currently spec'd as the ATWINC1500 (no stock), but the ATWINC3400 is available (as of this writing) and would work with very little system firmware updates.

The second biggest challenge is certification. Unforeseen issues can cause delays. I estimated 8 weeks which should be time to do 1 or 2 board spins if they are needed to pass FCC/CE certification testing.

