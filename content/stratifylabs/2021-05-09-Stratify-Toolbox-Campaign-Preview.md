---
categories:
- Stratify Labs
date: "2021-05-09"
layout: post
tags: [business, product development, stratifylabs, toolbox]
title: Stratify Toolbox Campaign Preview
---

This is a preview of the upcoming [Stratify Toolbox Crowd Supply Campaign](https://www.crowdsupply.com/stratify/toolbox).

Your [feedback](https://www.crowdsupply.com/stratify/toolbox/ask-question) is welcome!

> VIDEO PREVIEW COMING SOON

## Rewards

- Stratify Toolbox: 399USD
- Includes 10-Pin ARM SWD Breakout board

![Flash Tool Preview](/images/lvgl-flash-tool-preview.png)

> Rewards will show in the right panel

The Stratify Toolbox takes a new approach to **flashing and debugging microcontrollers**.

- **NO software to install!** It works on ***any*** device with a command-line
- **Super-easy** wifi setup using the touch display
- **Flashing** and `printf()` style **debugging**
- **Stand-alone** Production programming
- **Open-source SDK** for customizing any functionality of the Toolbox

![Overview Graphic](/images/toolbox-overview.svg)

## Focus on Ease-of-Use

**No Software to Install.**

- Use the **Touch** display for stand-alone operation
- **Type** `curl` commands interact with the HTTP server

Getting connected to Wifi is fast and simple using the touch screen.

{{< youtube onikaCopRm8 >}}
**Connect to Wifi**

### What MCUs are Supported?

**Flashing**

All STM32 Parts that can be programmed using STM32CubeProgrammer can be flashed using the Toolbox. 

**BYOBB.** **B**ring **Y**our **O**wn **B**inary **B**lob to support flashing any ARM Cortex-M chip. For most MCU's, the manufacturer provides a flash driver that you can easily integrate with Toolbox target template project.

Need support for a specific MCU? [Please let me know](https://www.crowdsupply.com/stratify/toolbox/ask-question).

**Debugging**

`printf()`-style trace debugging is supported on SWO, UART, and SPI serial protocols independent of the MCU. The output can be streamed to the command line using curl and/or written the SD card for retrieval later.

### Production Programming and Test

The Toolbox can be used for production programming. It can log the test and programming results on the SD card or upload it to a database using Wifi. With the [Toolbox SDK](#toolbox-sdk) you can completely customize the GUI application and test to suit your needs.

### Toolbox SDK

With the SDK, you can build and install custom applications to run on the Toolbox.

- **GUI Application**: build a customized test and/or flash programming application for production
- **flash/trace delegate**: add support for a UART or I2C serial bootloader protocol

The SDK includes high-level C++ APIs for:

- [JSON Handling](https://github.com/StratifyLabs/JsonAPI)
- [HTTP and HTTPS (using mbedtls) Client and HTTP Server](https://github.com/StratifyLabs/InetAPI)
- UART/SPI/I2C Drivers
- Analog Input (x2) and Output (x1) Drivers
- [SWD/JTAG plus ELF File Parsing](https://github.com/StratifyLabs/SwdAPI)
- [Graphics](https://github.com/StratifyLabs/LvglAPI) (based on [LVGL](https://lvgl.io/))
- [Cryptography Suite](https://github.com/StratifyLabs/CryptoAPI)
  - ECDSA: Sign code and verify signatures
  - ECDH Shared Secret: Establish a secure session with any target
  - AES 128/256: Fast Symmetrical Encryption
  - SHA256: create unique hashes for binaries and messages

All of the Stratify Toolbox applications are permissive open-source and freely available to modify.

The Toolbox runs on [Stratify OS](https://github.com/StratifyLabs/StratifyOS). It allows you to install applications that are built independent from the OS. So you can customize only the parts that you want. For example, if you want to replace the `Home` application (which is by default a webserver). You do:

```
export tb=http://<ip address>
curl -X PUT --data-binary @MyHome $tb/fs/home/bin/Home
```

If you ever need to revert to the default `Home` server, you just need to delete `/home/bin/Home` off the SD card.

## More Features

The Toolbox includes additional features to help with board bring-up and other development challenges.

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
| ------------------------------- | --------------------- | -------------- | --------------- |
| **Manufacturer**                | Stratify Labs, Inc    | Segger         | NXP             |
| **Price**                       | $399 ($499 retail)    | About $998     | About $610      |
| **Max. target interface speed** | 6 MHz                 | 15 MHz         | Unknown         |
| **Stand-alone operation**       | Yes                   | No             | Yes             |
| **PC software required**        | No                    | Yes            | Yes             |
| **SWD/JTAG**                    | Yes                   | Yes            | Yes             |
| **SWO**                         | 6 MHz                 | 30 MHz         | Yes             |
| **Supported voltage range**     | 1.8 V to 5.5 V        | 1.2 V to 5.5 V | 1.2 V / 5 V     |
| **Download speed into RAM**     | 0.8 MB/sec            | 1 MB/sec       | Unknown         |
| **SD card**                     | 4GB (card included)   | No             | With upgrades   |
| **Wi-Fi**                       | Yes                   | Yes            | No              |
| **Display**                     | IPS LCD w/ cap. touch | No             | LCD Touchscreen |
| **Customizable Web interface**  | Yes                   | No             | No              |
| **SDK**                         | Yes                   | No             | No              |
| **`printf()`-ing Awesome!**     | Yes                   | No             | No              |

## Support and Documentation

All the documentation you need to operate the Toolbox is served as a web page from Toolbox:

- Quick Start
- HTTP API Reference
- Instrumentation Specification

Support will be available through the usual channels. Plus, all the code will be published on Github.

## Manufacturing Plan, Fulfillment, and Logistics

Due to worldwide shortages, the manufacturing plan will take longer than usual. I can mitigate some of the schedule risks by replacing IC's with chips that are readily available. However, key components are 40 to 50 weeks out. The only remedy is to order them and wait.

The plan after funding is:

- Identify parts with long-lead times that have available, suitable alternates (power supplies, op-amps, etc) and order them
- Order the long-lead items with no alternates
- Order the plastic injected molds
- Update the design to accommodate alternates
- Build prototypes using the alternates
  - I have a few each of the long-lead items on hand for this
- Fabricate production PCBs
- Perform testing and certification using prototypes
- Iterate firmware, get feedback from beta testers (let me know if you want that to be you)
- Assemble production PCBs
- Final assembly and fulfillment

As it stands now, I should get all the parts by Q3 of 2022. That puts fulfillment at the end of Q3 or the beginning of Q4 of 2022.

## Risks and Challenges

The biggest challenge is procuring parts and integrating the alternates into the hardware/firmware. The crucial parts are the MCU and the Wifi module. The MCU can be one of several STM32H7 chips and still get the job done. Switching means updating the system firmware to work with the alternate chip. The Wifi is currently specified as the ATWINC1500 (no stock), but the ATWINC3400 is available (as of this writing).

The second biggest challenge is certification. Unforeseen issues can cause delays. But this effort can be performed while waiting for parts with long lead times.

> Version 6. Removed remote access section. Made various sections more concise. Moved versions to the end.

> Version 5. Removed secure programming (this will come later).

> Version 4. I removed the features for the web application. I never use that feature.

> Version 3. I removed the features highlighting instrumentation. I added a focus on the ability to do secure production programming.

> Version 2 