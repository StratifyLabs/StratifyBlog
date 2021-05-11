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

## The Problem

Programming microcontrollers is hard. It is tough to find and install the right software and get everything setup. The Stratify Toolbox takes a new approach to flashing and debugging microcontrollers.

- NO host software to install! It works on ANY device with a browser and/or a command-line
- Super-easy wifi setup using the touch display

**Designed for `printf()`-ers**

Rather than focusing on breakpoints, and code-stepping like most tools, the Stratify Toolbox is maximize the workflow of developers who prefer `printf()` serial tracing over code-stepping.

**Fixing `printf()`**

![Fixing Printf](/images/fixing-printf.png)

## Focus on Ease-of-Use

**No Software to Install.** There is NO HOST SOFTWARE to install. Last time, there is **no software to install**.

![Fixing Printf](/images/touch-type-click.png)

- Use the **Touch** display for stand-alone operation. 
- **Type** `curl` commands in the terminal to integrate with your workflow. 
- **Click** to use the embedded web server as a GUI for flashing and debugging.

Setup the Wifi in less than 60 seconds

[Connect to Wifi](https://youtu.be/ol9aOj6xnAg)

[Connect to target](https://youtu.be/b9TZABBLgmo)


### Instrumentation: Go beyond Debugging


| Debugging vs.             | Instrumenting             |
|--------------------------|---------------------------|
| Track single bugs        | See overall performance   |
| Check a variable's value | Plot a variable over time |
| Track a state change     | Draw a state diagram      |
| Check one packet         | Draw a sequence diagram   |


Turn this:

```
DIR:hist:Malloc Performance:malloc:Occurences vs amount of time spent executing malloc()
D:malloc:120
D:malloc:130
D:malloc:120
D:malloc:110
D:malloc:150
...
D:malloc:160
D:malloc:110
D:malloc:200
D:malloc:110
D:malloc:130
```

Into this:

![Malloc Performance Historgram](/images/ir-histogram.png)

**Export to Markdown or JSON**

View on Git* or import to Python for additional post-processing

SCREENSHOTS OF INSTRUMENTATION REPORTS ON GITHUB

### SDK

The Stratify Toolbox includes an SDK that allows you to customize anything. If you have a custom bootloader, you create your own flash delegate. You can then use that delegate in both development and production.

The default delegates include:

- Flash/SWD/STM32 (I am working on more flash delegates. Tell me what YOU want).
- SWO/text (supports ascii and `printf()` format off-loading)
- UART/text (supports ascii and `printf()` format off-loading)

## More Features

Not only is the Stratify Toolbox a fantastic flash/debug tool designed specifically for `printf()`-style tracing, it can be used as a stand-alone flash programmer, I2C scan tool, UART baud-rate detector, and more. If you aren't quick ready to back the Toolbox, be sure to sign up for the updates for more details and how the Toolbox can make it a little less hard to develop microcontroller firmware.

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

|                                 | Toolbox                                | JLink Base              | MultLink FX         |
|---------------------------------|----------------------------------------|-------------------------|---------------------|
| **Manufacturer** ~active        | Stratify Labs, Inc~active              | Segger ~active          | PE Micro ~active    |
| **Price**                       | $299 ($399 retail) ~success            | About $399 ~danger      | About $399 ~danger  |
| **Max. target interface speed** | 10 MHz ~danger                         | 15 MHz ~success         | Unknown ~info       |
| **Stand-alone operation**       | Yes ~success                           | No ~danger              | No ~danger          |
| **PC software required**        | No ~success                            | Yes ~danger             | Yes ~danger         |
| **SWD/JTAG**                    | Yes ~success                           | Yes ~success            | Yes ~success        |
| **SWO**                         | 8 MHz ~danger                          | 30 MHz ~success         | Yes ~info           |
| **Supported voltage range**     | 1.8 V to 5.5 V ~warning                | 1.2 V to 5.5 V ~success | 3.3 V / 5 V ~danger |
| **Download speed into RAM**     | 0.8 MB/sec ~success                    | 1 MB/sec ~warning       | Unknown ~info       |
| **SD card**                     | 4 GB (card included) ~info             | No ~info                | No ~info            |
| **Wi-Fi**                       | Yes ~success                           | No ~danger              | No ~danger          |
| **Display**                     | IPS LCD with capacitive touch ~success | No ~danger              | No ~danger          |
| **Customizable Web interface**  | Yes ~success                           | No ~danger              | No ~danger          |
| **SDK**                         | Yes ~success                           | No ~danger              | No ~danger          |
| **`printf()`-ing Awesome!**     | Yes ~success                           | No ~danger              | No ~danger          |

## Support and Documentation

All the documenation you need to operate the Toolbox is served from the web application.

TODO Make these 4 images a 2x2 matrix

![State Diagram](images/docs-quickstart.png)
![State Diagram](images/docs-reference.png)
![State Diagram](images/docs-trace1.png)
![State Diagram](images/docs-trace2.png)

If you want to use the SDK, check out these repositories

- [ToolboxAPI](): Repo for ToolboxAPI framework
- [ToolboxApps](): Repo for Graphical Application
- [ToolboxDelegates](): Repo for built-in delegates

## Manufacturing Plan, Fulfillment and Logistics

Due to the worldwide shortage of parts, the manufacturing plan is trickier than usual. Normally, I would just outsource to a turn-key PCB assembly house. But that would take a year. The plan is (best case scenario):

- Order crucial parts or alternates (first week)
- Order plastic injection molds and parts (first week)
- Order other mechanical parts such as screws, CE/cert stickers (first week)
- Update and test the design with the alternate parts (8 weeks)
- Test and cerfity the updated design (8 weeks)
- Order commodity parts and PCB's (3 weeks)
- Final assembly with domestic PCB assembly house (8 weeks)
- Send product to Crowd Supply for fulfillment (last week)

Using this plan, it is not unrealistic to deliver project in Q1 of 2022. The worst case scenario is that no alternate parts will be available for crucial components. In which case, the delivery date is pushed back to June or July of 2022.

As of this writing, there are alternate parts for crucial components in stock at major distributors.

## Risks and Challenges

The biggest challenge is getting parts and then making them work. The crucial parts are the MCU and the Wifi. The MCU can be a number of different STM32F7 or STM32H7 chips and still get the job done. But switching means updating the system firmware to work with the alternate chip. The Wifi is currently spec'd as the ATWINC1500 (no stock), but the ATWINC3400 is available (as of this writing) and would work with very little system firmware updates.

The second biggest challenge is certification. You just never know if you are going to come across some nagging issue that will cause delays. I estimated 8 weeks which should be time to do 1 or 2 board spins if they are needed to pass FCC/CE certification testing.

