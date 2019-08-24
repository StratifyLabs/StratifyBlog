---
categories:
- Device Tips
date: "2019-08-16"
layout: post
tags:
- microcontroller
- c/c++
- programming
title: Using Strong Type Arguments on Embedded C++
---

Using strong type arguments when writing embedded C++ code is a powerful tool for creating better code. I think of great code as being:

- Well written
- Well tested
- Well documented

Strong type arguments force the developer to write and document the code in one step. It also moves some of the
burden for debugging from the developer to the compiler by enforcing correct argument order which can ease
testing requirements.

### Weak Arguments

Let's consider a few examples of weak arguments.

```cpp
//prototypes with weak arguments
int beep(int count, int duration_on, int duration_off);
int set_rgb(int offset, int red, int green, int blue);
void copy_memory(const void * source, void * dest, int size);

//call-site
beep(5, 500, 500); //beep five times 500ms on and 500ms off
set_rgb(10, 255, 0, 0); //set LED 10 to red
char buffer0[16];
char buffer1[16];
copy_memory(buffer0, buffer1, 16); //copy buffer0 -> buffer1
```

On the code above, 

- The compiler will not care if the developer confuses the order at the call-site.

This can cause big headaches. In the case of an LED being the wrong color (`set_rgb()`), maybe that
just means digging through the code and fixing the error at the call-site. However for `copy_memory()`,
the bug may not manifest at the call site. You might end up spending a substantial amount of time
back tracing from where the bug is manifested to the `copy_memory()` call.

- Without the comments, it is not totally clear what the code is doing

Other developers will have to research the API to figure out what is going on. Also, the compiler
doesn't enforce accurate nor up-to-date comments. So even if comments are present, 
they can't necessarily be trusted in a debuggin scenario.

### Creating an Argument Template that Goes away with Optimization

The following code is a template for creating strong arguments that have no effect on
code size or run-time performance. The compiler enforces the arguments but none of the code makes
it to the final binary.

The code has been slightly modified from [Fluent C++'s Strong types for Strong interfaces](https://www.fluentcpp.com/2016/12/08/strong-types-for-strong-interfaces/).

```cpp
template <typename T, typename Tag>
class Argument {
public:
	explicit Argument(T const& value) : m_value(value) {}

	template<typename T_ = T>
	explicit Argument(T&& value,
							typename std::enable_if<!std::is_reference<T_>{},
							std::nullptr_t>::type = nullptr)
		: m_value(std::move(value)) {}

	T& argument() { return m_value; }
	T const& argument() const {return m_value; }
private:
	T m_value;
};
```

To create some arguments we use:

```cpp
using Count = Argument<int, struct CountTag>; 
```

which is equivalent to:

```cpp
typedef Argument<int, struct CountTag> Count;
```

### Strong Arguments

Using the `Argument` class above, we can update our weak argument code with strong arguments.

```cpp
//Strong arguments
//using class = is equivalent to typedef
using Count = Argument<int, struct CountTag>; 
using MillisecondsOn = Argument<int, struct MillisecondsOnTag>; 
using MillisecondsOff = Argument<int, struct MillisecondsOffTag>; 
using LedPosition = Argument<unsigned int, struct LedPositionTag>; 
using Red = Argument<unsigned int, struct RedTag>; 
using Green = Argument<unsigned int, struct GreenTag>; 
using Blue = Argument<unsigned int, struct BlueTag>; 
using SourceBuffer = Argument<const void*, struct SourceBufferTag>; 
using DestinationBuffer = Argument<void*, struct DestinationBufferTag>; 
using Size = Argument<int, struct SizeTag>; 

//prototypes with weak arguments
int beep(
    Count count, 
    MillisecondsOn duration_on, 
    MillisecondsOff duration_off
    );

int set_rgb(
    LedPosition offset, 
    Red red, 
    Green green, 
    Blue blue
    );

void copy_memory(
    const SourceBuffer source, 
    DestinationBuffer dest, 
    Size size
    );

//call-site
beep(
    Count(5), 
    MillisecondsOn(500), 
    MillisecondsOff(500)
    ); 

set_rgb(
    LedPosition(10), 
    Red(255), 
    Green(0), 
    Blue(0)
    ); 

char buffer0[16];
char buffer1[16];

copy_memory(
    SourceBuffer(buffer0), 
    DestinationBuffer(buffer1), 
    Size(16)
    );
```

With strong arguments we get.

- Code argument order cannot be confused.

For `copy_memory()`, the developer can still mix up the source and destination
but not it is much more difficult because they have to type `DestinationBuffer()`
with the source placed in parentheses. The compiler will not produce the code
if `SourceBuffer()` and `DestinationBuffer()` are out of order.

- Code is easily understandable by simply seeing it at the call-site.

On `set_rgb()`, it is obvious the position 10 LED is being set to red. There
is no need to add any comments stating that. The compiler forces the developer
to explicitly state what is going on. Also, saying `Red(255), Green(0), Blue(0)`
is much easily for your brain to see that is red than `255, 0, 0` even if you
are familiar with the API.

## Why You Should Do This

It may seem like overkill to strongly type every argument. But when you consider
that this technique is not just about writing code, you will see it has lasting long-term
value. Applied organizationally, strong arguments:

- Force every developer to add compiler-enforced clarity to all calls
- Save substantial debugging effort by having the compiler correct argument order confusion
- Makes the call-site code easier to learn and understand

When I first converted to strong argument types, I wondered if it was worth it as I went through
and spent hours and hours refactoring code. Now, I would never go back.
