---
categories:
- Product Management
date: "2019-04-28"
layout: post
tags: [business, product development]
title: Why Your Firmware is Behind Schedule?
---

Firmware development has always been a challenging endeavor, but recent industry trends have made it much more difficult to get firmware out the door on time. I want to explore a few of these trends and how Stratify Labs can help.

What's happening?

- Hardware is becoming more capable and more complex
- Software requirements are expanding due to IoT requirements

Unfortunately, the way we have approached developing firmware for many decades doesn't scale well with this increasing complexity.

## Why is firmware so hard in the first place?

Programming a microcontroller (firmware) is much more difficult than programming a computer (software) because there are more things that can go wrong. You can have:

- Hardware Design Errors
- Manufacturing Errors
- Firmware Errors

That ends up being a lot of variables to manage. The more your engineers can isolate variables and solve one issue at a time, the faster they will get things working.

**Hardware Design Errors**

The best firmware engineers to deal with these are the ones that have a solid harware background and can understand the schematic and its implications on the firmware. If possible, your firmware engineers should be included in the hardware design process and given the chance to review the proposed design.

Another good way to mitigate these errors is to develop the firmware on known good hardware (as in off-the-shelf development boards) then port the firmware over to experimental hardware.

**Manufacturing Errors**

Even if the design is correct, manufacturing prototypes is prone to error. The manufacturer can misunderstand the documents. The distributor can put the wrong parts in the bag. The PCB fabrication process itself can be erroneous.

It is usually left to the firmware engineers come across these errors and figure out exactly what went wrong. Again, having firmware engineers that understand hardware is key. 

Also, building more than one board in a batch of prototypes can be helpful. If only one board is having problems, that usually points to a PCB assembly/fabrication error. If all the boards have an identical problem, that usually means a design error or a manufacturer misunderstood the design.

**Firmware Errors**

When programming a microcontroller, the chip doesn't do anything at all until it is programmed. Sometimes it is a challenge just to get the board to accept firmware (due to the above error types). Once the firmware is loaded, how the firmware interacts with the hardware can cause the firmware to stall or crash or behave in unexpected (and baffling) ways.

The best approach is a layered, methodical approach. Firmware engineers start by getting low-level drivers working. Many times these drivers are provided by the microcontroller manufacturer (many times these same drivers have bugs). Each driver should be unit tested and certified to work robustly before developing the next layer of firmware.

After the drivers are done, middleware and helper libraries are integrated. If these are not known to be stable and reliable, unit testing should be done on these as well.

Lastly, the application is developed. If everything below the application is well tested, the development goes much more smoothly when experimental code is limited only to the application layer.

Even with this approach, firmware engineers need to constantly be thinking about how resources are being utilized in order to avoid tricky memory bugs and achieve real-time performance.

**Putting it Together**

If the application is simple (like they were twenty years ago), the same hardware-focused firmware engineer can handle the whole project. However, with more complex chips and more complex software requirements (over-the-air updates, security, TCP/IP stacks, displays, etc, etc), a good firmware engineering teams needs:

- A hardware-focused firmware engineer to tackle hardware design and manufacturing problems
- A microarchitecture-focused firmware engineer to ensure the processor meets the real-time requirements of the application
- An object-oriented-software-focused firmware engineer to design scalable, reusable architectures that can meet the complex software requirements driven by IoT

A very small percentage of firmware developers have a proficiency in the above three categories. Some can do two well. This means your firmware team needs either one very talented engineer or two to three (or more) that can cover the array of skills needed and work well together as a team.

If you are missing a hardware-focused engineer, you will have a hard time getting your firmware to interact with the hardware and may end up with finger pointing between hardware and firmware engineers.

If you are missing the software-focused engineer, you will end up with code that is extremely difficutl to maintain and manage long term.

If you are missing the microarchtecure-focused engineer, you will have a hard time getting the microcontroller to do exactly what you want when you want.

When you retry to put it altogether, it is a very difficult and challenging endeavor indeed. I have witnessed projects that are taking many months or even years longer than expected due to these skills not coalescing the way they need to.

## How Stratify Labs can Help

Stratify Labs licenses and supports a high level operating system for microcontrollers called Stratify OS. Using Stratify OS greatly reduces the skills you need in house. It handles the microarchitecture and completely abstracts the hardware leaving just the software application up to your team. It basically turns a challenging firmware problem into a straightforward software problem.





