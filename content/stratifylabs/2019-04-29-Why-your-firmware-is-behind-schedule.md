---
categories:
- Product Management
date: "2019-04-29"
layout: post
tags: [business, product development, stratifylabs]
title: Why Your Firmware is Behind Schedule
---

Firmware development has always been a challenging endeavor, but recent industry trends have made it much more difficult to get firmware out the door on time. I want to explore a few of these trends and how Stratify Labs can help.

> **Defining Firmware** Firmware, per se, does not have a nice clear definition. It is something between hardware and software. For the purposes of this post, firmware is the code that runs on microcontrollers.

**What's Happening?**

- Hardware is becoming more capable and more complex
- Software requirements are expanding due to IoT requirements

Historically, firmware has been developed as a monolithic program that runs on microcontrollers with 10's of kilobytes of program memory running at low megahertz speeds. This was great for [microcontrollers]({{< ref "device/2013-10-14-how-microcontrollers-work.md" >}}) used in old-school alarm clocks and other simple devices. Now that microcontroller memory measures in the 100's of kilobytes or even megabytes and run at 10's or hundreds of megahertz, it is more difficult to use the historical approach. These microcontrollers are powering a whole new generation of connected devices from smart alarm clocks to smart watches and much more. But before we talk about how to handle this problem, let's dive deeper into what makes this such a challenging problem.

## Why is firmware so hard in the first place?

Programming a microcontroller (firmware) is much more difficult than programming a computer (software) because there are more things that can go wrong. You can have:

- Hardware Design Errors
- Manufacturing Errors
- Firmware Errors

That ends up being a lot of variables to manage. The more engineers can isolate variables and solve one issue at a time, the faster they will get things working.

**Hardware Design Errors**

The best firmware engineers to deal with these are the ones that have a solid harware background and can understand the schematic and its implications on the firmware. Firmware engineers should be included in the hardware design process and given the chance to review the proposed design to ensure debugging hardware will be as simple as possible.

Another good way to mitigate these errors is to develop the firmware on known, working hardware (as in off-the-shelf development boards) then port the firmware over to experimental hardware. So you have:

- Experimental firmware on known-working hardware (isolate firmware issues)
- Known-working firmware on experimental hardware (isolate hardware issues)

**Manufacturing Errors**

Even if the design is correct, manufacturing errors can and do occur. The manufacturer can misunderstand the documents. The distributor can put the wrong parts in the bag. The PCB fabrication process itself can be erroneous.

> I mention the above issues because I have personally seen them happen. Some errors are avoidable and some are not. So you need to be prepared to deal with them.

It is usually left to the firmware engineers to come across these errors and figure out exactly what went wrong. Again, having firmware engineers that understand hardware helps a lot. 

Also, building more than one board in a batch of prototypes can be helpful. If only one board is having problems, that usually points to a PCB assembly/fabrication error. If all the boards have an identical problem, that is usually a design error.

**Firmware Errors**

When programming a microcontroller, the chip doesn't do anything at all until it is programmed. Sometimes it is a challenge just to get the board to accept firmware (due to the above error types). Once the firmware is loaded, how the firmware interacts with the hardware can cause the firmware to stall or crash or behave in unexpected (and baffling) ways.

The best way to minimize tricky-to-solve firmware errors is to take a layered approach. Start by getting low-level drivers working. Many times these drivers are provided by the microcontroller manufacturer (many times these drivers have bugs). Each driver should be unit-tested and working robustly before developing the next layer of firmware.

After the drivers are done, you can develop middleware and helper libraries. If these are not known to be stable and reliable, unit testing should be done on these as well (preferably on known-working hardware). They should also be integration tested with the low-level drivers as applicable.

Lastly, the application is developed. If everything below the application is well tested, the development goes much more smoothly because experimental code is limited to the application layer.

Even with this approach, firmware engineers need to constantly be thinking about how resources are being utilized in order to avoid tricky memory bugs and achieve the desired real-time performance.

> **Compared to Software** Software is so much easier to develop because it is always running on known-working hardware. Also, the operating system provides robust and comprehensive tools for running threads, interacting with devices, storing and retrieving data from the filesystem, managing memory, and debugging. So out of the box, software starts with the application on top of a well-tested operating system while firmware starts with debugging the hardware design then building the code from source.

**Putting it Together**

If the application is simple (like they were twenty-plus years ago), the same hardware-focused firmware engineer can handle the whole project. However, with more complex chips and more complex software requirements (over-the-air updates, security, TCP/IP stacks, displays, etc), a good firmware engineering teams needs:

- A hardware-focused firmware engineer to tackle hardware design and manufacturing problems
- A microarchitecture-focused firmware engineer to ensure the processor meets the real-time requirements of the application
- An software-focused firmware engineer to design scalable, reusable software that can meet the complex requirements driven by internet connectivity

A very small percentage of firmware developers have a proficiency in the above three categories. Some can do two well. This means your firmware team needs either one very talented engineer or two to three (or more) that can cover the array of skills needed and work well together as a team. If you are missing a hardware-focused engineer, you will have a hard time getting your firmware to interact with the hardware and may end up with finger pointing between hardware and firmware engineers. If you are missing the software-focused engineer, you will end up with code that is extremely difficult to maintain and manage long term. If you are missing the microarchtecure-focused engineer, you will have a hard time getting the real-time performance you need.

When putting it all together, it is a very difficult and challenging endeavor indeed. I have witnessed projects that are taking many months or even years longer than expected due to these skills not coalescing in the right way and causing needless (and time-consuming) hardware revisions.

## How Stratify Labs can Help

Stratify Labs licenses and supports a high level operating system for microcontrollers called Stratify OS which turns a firmware problem into a software problem. Unlike other microcontroller operating systems (commonly called an RTOS), Stratify OS completely abstracts the hardware away providing firmware application developers with:

- Device Drivers
- Memory Management
- Filesystems
- Debugging Tools
- Over-the-internet updates
- Networking Stacks

Using Stratify OS greatly reduces the firmware skills required to build sophisticated internet connected devices on microcontrollers. 

- Complete hardware abstraction minimizes the need for hardware-focused engineers
- Built-in memory and task management negates the need to have microarchitecture-focused enginners
- Supporting hardware-independent applications written in C++ gives object-oriented software programmers the tools they need to be effective

## What to do now?

If you are interested in learning more about Stratify OS and how it can streamline your IoT development, please [request a demo](https://stratifylabs.co).






