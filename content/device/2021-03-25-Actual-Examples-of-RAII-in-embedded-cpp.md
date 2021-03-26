---
categories:
- Device Tips
date: "2021-03-25"
layout: post
tags:
- microcontroller
- cpp
- programming
title: Actual Examples of RAII in embedded C++
---

I have been using C++ in embedded microcontroller applications for over 10 years. Recently, I re-wrote all the frameworks I use to better leverage modern C++. One of the big changes was using RAII wherever possible. I have a compilation of actual use cases where RAII has been awesome.

But first:

- What is embedded c++? [Read about it.]({{< relref "2020-12-23-What-is-Embedded-Cpp.md" >}})
- What is RAII? [Read about it.]({{< relref "2020-12-22-RAII-Everywhere-in-Cpp.md#what-is-raii" >}})

# Using RAII in the Real World

RAII lets you undo something you do without thinking about it. This comes in really handy when working on embedded systems: close something you opened, turn off something you turned on, free something you allocated, unlock something you locked and so on.

## Critical Code Contexts

What is your approach for critical code sections that can't be interrupted? Check out this `CriticalContext` class and example. RAII will automatically turn interrupts back on when `critical_context` goes out of scope. This means you don't need to worry about re-enabling interrupts in multiple places.

```c++
class CriticalContext {
public:
  CriticalContext(){ disable_interrupts(); }
  ~CriticalContext(){ enable_interrupts(); }
}

void my_critical_function(int value){
  CriticalContext critical_context;

  switch(value){
    case 0:
    // do something then abort
    return;
    case 1:
    // do something else
    break;
    case 2:
    // another thing
    break;
    default:
    break;
  }

  if( value > 1000 ){
    return;
  }

  //do the last thing
  return;
}
```

## Power Mangement

You can also use RAII to guarantee you turn off a device when you are done accessing it. Say you have a `GPIO` line that controls a power source that you only want enabled when you are accessing a device. RAII can automatically turn off the power when the context goes out of scope.

```c++
class PowerContext {
public:
  PowerContext(){ enable_power(); }
  ~PowerContext(){ disable_power(); }
}

int my_function(){
  PowerContext pc;
  return read_sensor();
}

```

## Performance Profiling

Have you ever needed to track how long snippets of code take to execute. You can use RAII.

```c++
class PerformanceTimerContext {
public:
  PerformanceTimerContext(const char * name) : m_name(name){ 
    printf("start %s context --->\n", name);
    m_timer.start(); 
  }
  ~PerformanceTimerContext(){ 
    timer.stop(); 
    printf("stop %s context: %ld us--->\n", , m_timer.microseconds());
  }

private:
  const char * m_name;
  Timer m_timer;
}

void my_function(){
  PerformanceTimerContext ptc("file");
  int fd;
  {
    PerformanceTimerContext ptc("openFile");
    fd = open("/home/my_file.txt", O_RDWR);
  }

  {
    char buffer[64];
    PerformanceTimerContext ptc("readFile");
    read(fd, buffer, 64);
  }

    {
    PerformanceTimerContext ptc("closeFile");
    close(fd);
  }
}
```

## And More

By now, I think you have gotten the idea. RAII is used extensively in the Stratify [API](https://github.com/StratifyLabs/API).  Other examples include:

- [Opening/Closing Files](https://github.com/StratifyLabs/API/tree/main/libraries/FsAPI)
- [Lock/Unlocking a Mutex](https://github.com/StratifyLabs/API/blob/main/libraries/ThreadAPI/include/thread/Mutex.hpp)
- [Printing Structured Data](https://github.com/StratifyLabs/API/blob/main/libraries/PrinterAPI/include/printer/Printer.hpp)
- [Managing Thread Lifetimes](https://github.com/StratifyLabs/API/tree/main/libraries/ThreadAPI)
- [Testing](https://github.com/StratifyLabs/API/blob/main/libraries/TestAPI/src/Case.cpp)
