---
categories:
- Product Management
date: "2020-05-18"
layout: post
tags:
- product development
- business
title: Selecting a chip for your next IoT Product Microprocessors vs Microcontrollers
---

There are many things to consider when choosing a computing chip for your next IoT product. The biggest choice to make is whether to use a microprocessor or microcontroller.

Generally, the definitions of microprocessor and microcontroller are not univerally accepted. For the purposes of this post, a microprocessor is a chip that is capable of running a full-fledged operating system such as Linux (including embedded Linux), MacOS, Windows, iOS, or Android. Conversely, a microcontroller is an inexpensive computing device running which runs a single program or RTOS. 

For sophisticated products with complex graphics (like a tablet), the only viable option is a microprocessor. For very simple products (like fidget spinners), only a microcontroller is practical. For everything in between, there are trade-offs largely driven by whether you want the software or the hardware to be the biggest challenge.

## Microprocessors

The software for microprocessors is generally easy to develop while the hardware is quite challenging.

Microprocessor software benefits from having built-in, well-tested programs, filesystems, device drivers and task management. For example, on embedded linux, you can easily download a file from the internet to the local filesystem using tools that are readily available without writing any code.

The biggest challenges with microprocessors come with the hardware which is both difficult to acquire for production and difficult to design during development.

Most microprocessors are sold directly to companies by the manufacturer rather than through electronics distributors (such as Digikey and Mouser). The manufacturers typically only work with companies that are producing a very high volumes. This sales model makes it difficult for small companies and startups to get access to the best products on the market. Even once you have found a chip that can be purchased for production, there are big hardware design challenges.

Microprocessors require sophisticated high-speed circuits which means hiring experienced engineers with specific skills. It is also a big challenge to get embedded Linux running on a custom board once it has been designed. Most small companies solve this using a relatively expensive off-the-shelf single board computer (such as the Raspberry pi or beagle bone). A microcontroller is another way around these challenges.

## Microcontrollers

Microcontrollers are highly integrated computing devices that do not require sophisticated circuit designs to integrate into an IoT product. There are literally thousands of chips on the market that can be readily purchased in just any quanities from electronics distributors. One of the biggest driving factors in choosing a specific chip is the preference of your firmware engineers. Because unlike microprocessors, [writing software for microcontrollers, also known as firmware, is very challenging]({{< ref "2019-04-29-Why-your-firmware-is-behind-schedule.md" >}}).

Because microcontrollers do not typically run an operating system, the application software must perform all the operating system tasks. If we consider the above example of downloading a file from the internet, it is much more difficult on a microcontroller. Before the download can happen, the microcontroller needs a filesystem, networking stack, internet (ethernet/wifi/cellular) hardware drivers, and a way to manage the memory and tasks. The classic approach to this is to use 3rd party libraries to integrate all the source code together and produce one big program to handle everything. The way we, at Stratify Labs, approach this challenge is by installing Stratify OS to handle all the standard computing tasks and allowing high level applications to handle the specifics.

> [Stratify OS](https://docs.stratifylabs.co) is a microcontroller operating system that completely abstracts away the hardware making it tens times easier to develop and deploy sophisticated IoT products. You can [request a demo](https://stratifylabs.co) if you want to learn more.



