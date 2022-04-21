---
categories:
- Device Tips
date: "2022-05-01"
layout: post
katex: true
chart: true
tags:
- cpp
- programming
- microcontroller
- cortex-m
- linux
title: An Everywhere GUI Stack in C++
---

After many years of trying all kinds of different approaches, I finally found an open-source software stack that lets me create internet-connected, GUI applications on any device including:

- Bare Metal Microcontrollers
- Embedded Linux including Raspberry Pi
- Windows
- macOS
- Ubuntu
- Android and iOS (these are still a work in progress)

My first foray with GUI programming on a professional level came using the Qt framework (around 2009). I loved that it was written in C++ because as an embedded developer I didn't have to learn a new language. Qt has been migrating away from its open-source roots. So I have been investigating alternatives for several years. I have dabbled in and investigated all kinds of alternatives from react native, to flutter, to roll-your-own. Unfortunately, the top cross-platform frameworks are not microcontroller friendly and are not written in C++.

I had a big breakthrough when I started digging into the [LVGL](https://lvgl.io/) project. LVGL aka Light and Versatile Graphics Library is an open-source C project designed for building rich GUIs on microcontrollers. LVGL with some help from the [SDL aka Simple Direct Media Layer](https://www.libsdl.org/) can run on any platform that SDL supports including Windows, macOS, Linux (desktop and embedded), iOS, and Android.

To connect the GUI to the internet, I use:

- [LWIP](https://savannah.nongnu.org/projects/lwip/): this gets you BSD style socket on microcontrollers (not needed on OS platforms)
- [mbedtls](https://tls.mbed.org/): microcontroller friendly TLS library that can run just about anything
  - as a bonus, mbedtls gives you a nice cryptography library that can be used for various applications
- [jansson](https://jansson.readthedocs.io/en/latest/): microcontroller friendly JSON library (a JSON library is essential for most internet operations)


To summarize, the stack includes:

- LVGL
- SDL (not needed on microcontrollers)
- LWIP (not needed on OS platforms)
- mbedtls
- jansson


## Making it C++

It is not trivial to make five independent C libraries build and work together seamlessly. [Using the magic of CMake and git]({{< relref "2021-9-29-CMake-Dev-Ops" >}}), we can make it work. These libraries are written in C, but I like to write applications in C++. Rather than make calls to different libraries with various naming schemes, I have written C++ wrappers that make everything look like it was written using the same coding approach.

- [DesignAPI](https://github.com/StratifyLabs/DesignAPI) adds theme management to LVGL
- [LvglAPI](https://github.com/StratifyLabs/LvglAPI) wraps LVGL
- [WindowAPI](https://github.com/StratifyLabs/WindowAPI) wraps SDL
- [InetAPI](https://github.com/StratifyLabs/InetAPI) wraps BSD sockets with mbedtls built-in
- [JsonAPI](https://github.com/StratifyLabs/JsonAPI)
- [API](https://github.com/StratifyLabs/API) base library for filesystem, threads, data, printing

## The GUI

We bring everything together using the [sample GUI application](https://github.com/StratifyLabs/gui). It can be built on Windows, macOS, and Linux (including Raspberry Pi) using a few simple commands:

```sh
git clone https://github.com/StratifyLabs/gui.git
cd gui
cmake -P bootstrap.cmake
cd cmake_link
cmake .. -GNinja
ninja sdl
rm -Rf *
cmake .. -GNinja
ninja
```

> If you are building on Raspberry Pi, see the [instructions for creating an image on Github](https://github.com/StratifyLabs/gui/blob/main/RPI.md).

The dependency libraries are automatically cloned, configured, and built using CMake and git.

Here is what the home screen looks like on macOS:

![GUI on MacOS](/images/gui-macos.png)

Here is a video demo running on a Raspberry Pi 4:

{{< youtube 8QnP54cFU_c  >}}

It can also run on an [STM32H735G-DK](https://github.com/StratifyLabs/STM32H735G-DK) development board that is running [Stratify OS](https://github.com/StratifyLabs/StratifyOS).


![GUI on STM32H735G-DK](/images/gui-stratifyos-1.png)

## Licensing

All these libraries have permissive open source licenses (BSD, MIT, apache, etc). All the Stratify Labs API libraries are licensed under the MIT license. If you are interested in contributing to any of them, please let me know (hello at stratifylabs dot co).

## To Do

This stack works great on everything from microcontrollers to desktop applications. I haven't needed to build and run on iOS or Android so I am not sure if that will work. But SDL runs on those platforms. So it should be possible.

I use this stack for simple firmware update applications. It probably isn't the best solution if you are distributing a complex application to millions of users. But it is great for simple tools either used internally or distributed to users.

I hope you find these libraries as useful as I have!

