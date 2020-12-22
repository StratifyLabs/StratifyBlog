---
categories:
- Device Tips
date: "2019-08-16"
layout: post
tags:
- microcontroller
- cpp
- programming
title: Using Strong Type Arguments in Embedded C++
---

Using strong arguments is part of a larger embedded C++ philosophy outlined in the following posts.

- [Thread Local Error Contexts]({{< relref "2020-12-14-Thread-Local-Error-Context-in-Cpp.md" >}})
- [Method Chaining]({{< relref "2020-12-15-Method-Chaining-in-Cpp.md" >}})
- **Strong Arguments**
- [RAII Everywhere]({{< relref "2020-12-22-RAII-Everywhere-in-Cpp.md" >}})
- Filesystems Inspired Abstraction

Using strongly type arguments when writing embedded C++ code is a powerful tool for creating better code. I think of great code as being:

- Well written
- Well tested
- Well documented

Strong type arguments force the developer to write and document the code in one step. It also moves some of the burden for debugging from the developer to the compiler by enforcing correct argument order which can ease testing requirements.

## Weak Arguments

Let's consider a few examples of weak arguments.

```c++
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

This can cause big headaches. In the case of an LED being the wrong color (`set_rgb()`, maybe that just means digging through the code and fixing the error at the call-site. However for `copy_memory()`, the bug may not manifest at the call site. You might end up spending a substantial amount of time back tracing from where the bug is manifested to the `copy_memory()` call.

- Without the comments, it is not totally clear what the code is doing

Other developers will have to research the API to figure out what is going on. Also, the compiler doesn't enforce accurate nor up-to-date comments. So even if comments are present, they can't necessarily be trusted.

## Using Classes for Strong Arguments

To avoid argument ambiguity, I like to use a nested class to define the arguments to a method. I use a setter/getter macro to make it easier to define these classes.

Here is a macro for getting/setting a fundamental value with a [method chaining]({{< relref "2020-12-15-Method-Chaining-in-Cpp.md" >}}) setter.

```c++
#define API_ACCESS_FUNDAMENTAL( \
    CLASS_NAME, \
    TYPE_NAME, \
    VALUE_NAME, \
    INITIAL_VALUE) \                                    
public:             \                                                           
  TYPE_NAME VALUE_NAME() const { return m_##VALUE_NAME } \
  CLASS_NAME &set_##VALUE_NAME(TYPE_NAME value){ \
      m_##VALUE_NAME = value; \
    return *this; \
    } \                                                          
private: \                                                          
  t m_##v = iv \
```

Now, we take take the weak examples above and make them strong.

```c++
class Beep {
  API_ACCESS_FUNDAMENTAL(Beep,int,count,5);
  API_ACCESS_FUNDAMENTAL(Beep,int,duration_on,100);
  API_ACCESS_FUNDAMENTAL(Beep,int,duration_off,100);
};
int beep(const Beep & options);

class SetRgb {
  API_ACCESS_FUNDAMENTAL(SetRgb,int,offset,0);
  API_ACCESS_FUNDAMENTAL(SetRgb,int,red,255);
  API_ACCESS_FUNDAMENTAL(SetRgb,int,green,255);
  API_ACCESS_FUNDAMENTAL(SetRgb,int,blue,255);
};
int set_rgb(const SetRgb & options);

class CopyMemory {
  API_ACCESS_FUNDAMENTAL(CopyMemory,const void*,source,nullptr);
  API_ACCESS_FUNDAMENTAL(CopyMemory,void *,dest,nullptr);
  API_ACCESS_FUNDAMENTAL(CopyMemory,int,size,0);
};
void copy_memory(const CopyMemory & options);
```

Now when we call the strong versions, it looks like this:

```c++
beep(Beep().set_count(5).set_duration_on(500).set_duration_off(500));
set_rgb(SetRgb().set_offset(10).set_red(255).set_blue(0).set_green(0));
char buffer0[16];
char buffer1[16];
copy_memory(CopyMemory().set_source(buffer0).set_destination(buffer1).set_size(16));
```

We end up with code that is

- more clear
- more concise
- harder to misuse

## Not for Every Method

Using this approach for every single method gets old (fast). I only use this approach in two situations.

1. There is no other way to differentiate between types. For example, a method takes two `const char*`.
2. The method has too many arguments (usually more than 3).

Here are some examples (from the [API Framework](https://github.com/StratifyLabs/API)):

```c++
//both of these types are strong
//just two arguments
//no need for an options class
File(const var::StringView path, OpenMode mode);

//takes two arguments that are the same type
//need to minimize the chance of
//confusing the argument order
class Rename {
  API_ACCESS_FUNDAMENTAL(Rename,const char*,old_path,nullptr);
  API_ACCESS_FUNDAMENTAL(Rename,const char*,new_path,nullptr);
};
void rename(const Rename & options){
  //assert to force proper usage of the method
  API_ASSERT(options.old_path() != nullptr);
  API_ASSERT(options.new_path() != nullptr);
  ::rename(options.old_path(), options.new_path());
}

class Write {
  public:
    API_ACCESS_FUNDAMENTAL(Write, int, location, -1);
    API_ACCESS_FUNDAMENTAL(Write, u32, page_size, 4096);
    API_ACCESS_FUNDAMENTAL(Write, size_t, size, 0);
    API_ACCESS_FUNDAMENTAL(Write, char, terminator, 0);
    API_ACCESS_FUNDAMENTAL(Write, chrono::MicroTime, timeout, 0_microseconds);
    API_ACCESS_FUNDAMENTAL(
      Write,
      chrono::MicroTime,
      retry_delay,
      10_milliseconds);
    API_ACCESS_FUNDAMENTAL(
      Write,
      const var::Transformer *,
      transformer,
      nullptr);
    API_ACCESS_FUNDAMENTAL(
      Write,
      const api::ProgressCallback *,
      progress_callback,
      nullptr);
  };

//write the contents of source to this file
//too many options for using them inline
int File::write(const File & source, const Write & options = Write());
```

## Why You Should Do This

It may seem like overkill to strongly type every argument. But when you consider
that this technique is not just about writing code, you will see it has lasting long-term value. Applied organizationally, strong arguments:

- Force every developer to add compiler-enforced clarity
- Save substantial debugging effort by having the compiler correct argument-order-confusion
- Makes the call-site code easier to learn and understand

When I first converted to strong argument types, I wondered if it was worth it as I went through and spent hours and hours refactoring code. Now, I would never go back.
