---
categories:
- Device Tips
date: "2019-07-11"
layout: post
tags:
- microcontroller
- programming
- c/c++
title: Double vs Float in Embedded C
---


C and C++ have two native types that are used for working with floating point values.

- `double` is 64-bits
- `float` is 32-bits

In many API's in the standard C library, the default type is a `double` which is great on 64-bit chips but not so for microcontrollers. Some build systems will downgrade `double` to a 32-bit value for microcontrollers. However, it is still problematic because on microcontrollers with a floating point unit or FPU, the `double` type will be calculated using software using bloated routines while `float` values will be computed very quickly in hardware.

Whether `double` is 64-bit or 32-bit, you can save a good amount of code space by avoiding it.

## How to avoid double

Avoiding `double` is trickier than you might think. Consider this code snippet.

```
float x = 1.0; //1.0 is a double and converted to float for x
float y = 1.0f; //1.0f is already a float and needs no conversion
```

Here is another one.

```
float x = sin(3.14f); //sin() uses doubles so there is a lot of converting
float y = sinf(3.14f); //all floating point values here
```

Now one more.

```
float x = 3.14f;
printf("x is %0.2f\n", x); //printf() uses double
```

But in this case, there is no easy solution to avoid `double`. `printf()` uses double when formatting. So unless you have access to modify the C library, you need a more creative workaround.

```
float x = 3.14f;
printf("%ld.%02ld\n", lroundf(x), (x - lroundf(x)) * 100.0f);
```

> **Note.** If you avoid `%f` in `printf()` and are using newlib, you can use the `iprintf()` family of functions and save on some code size.

## Good Luck

I can't list out all the possible ways to avoid `double` but hopefully the examples above will get you thinking the right way so you can scrub any `double` values from your code. And if you are using [Stratify OS](https://stratifylabs.co/) on an FPU enabled chip, all the scrubbing is done for you.
