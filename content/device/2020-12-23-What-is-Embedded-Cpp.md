---
categories:
- Device Tips
date: "2020-12-23"
layout: post
tags:
- microcontroller
- cpp
- programming
title: What is Embedded C++ 
---

In many of my posts, I refer to Embedded C++. It turns out, there is no formal definition. I would like to put forth what it means in the context of these posts.

## Narrowing it Down

C++ is an enormous language. Using polymorphism, you can write a language within the language and then languages within that language. As a result, some companies or projects will narrow the scope of C++ by codifying various practices. One such common practice in constrained systems is disabling exceptions and real-time type information. Another might be to prohibit the use of virtual methods or lambdas.

One reason narrowing C++ down is useful is that most embedded engineers are hardware/software experts that simply don't have the bandwidth to understand everything the C++ language can do. So focusing on the parts of C++ that are easy-to-learn if you know C (which all embedded engineers do) and provide substantial benefits over C can create a big jump in software quality.

Embedded C++ is, then, a narrowed down use case of C++ targeting embedded systems. The ultimate practical use is then "implementation-defined".

## Embedded C++ per Stratify Labs

The code I write is almost always for ARM Cortex-M chips with a minimum of 128KB of RAM and running at 10's to 100's of MHz. My embedded C++ definition is designed to fit into this execution context.

Embedded C++

- Exceptions Disabled
- Real-Time Type Information Disabled
- Minimize the usage of dynamic memory allocation
- Design APIs that are easy for C++ beginners to use and understand
  - Keep templating simple
  - Prefer C-style callbacks
- Abstract complex `std` features into easier to use and hardware to break APIs

## How do you define Embedded C++

It is really up to you to figure out which parts of the language will work best with both the hardware you are targeting and the knowledge and skills of your team implementing the code. Nonetheless, you will fare better if you narrow down C++ to fit your definition of *Embedded C++*.




