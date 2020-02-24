---
categories:
- Device Tips
date: "2013-10-12"
layout: post
page_source: MicrocontrollerBlog
tagline: Embedded Design
tags:
- microcontroller
- cortex-m
title: Effective Use of ARM Cortex-M3 SVCall
---

The ARM Cortex-M service call (SVCall) can be a tricky feature to integrate in to your system software.

## SVCall Intro

The SVCall (contraction of service call) is a software triggered interrupt. It is useful for: 

- Allowing a piece of code to execute without interruption
- Jumping to privileged mode from unprivileged mode

First, depending on interrupt priorities, the handler can be uninterruptible by one interrupt but interruptible by another. For example, if you have a piece of code that should not be interrupted by the timer but can be interrupted by the UART, you can set the interrupt priorities appropriately.

Second, if you are using the MPU or privileged mode on the ARM Cortex-M, the SVCall provides the code executing in unprivileged mode a way to access privileged resources.

## How the SVC Instruction Works

The SVC instruction invokes the service call interrupt. The bottom 8-bits of the SVC instruction can be set to any value and then interpreted by the interrupt handler. This essentially allows the user a way to execute 256 different types of service calls. 

I find using bottom 8-bits tedious because the service routine needs to first find the location of the SVC instruction when also lookup the value. I prefer to just put parameters on the stack that are pushed automatically by the NVIC. The sample code below shows how to do this using two parameters.

## SVCall Sample Code

To effectively use the service call interrupt, we pass two arguments to a function which immediately invokes the SVC instruction. The arguments are a pointer to a function to execute in privileged mode and a pointer to a data structure that the function can use to read/write data in the caller's context. The following code shows the prototype and body of the function.

```c++
//we need to decrease the optimization so the the compiler
//does not ignore func and args
void service_call(void (*func)(void*), void* args) __attribute__((optimize("1"));

void service_call(void (*func)(void*), void* args){
     //by convention func is in r0 and args is in r1
     asm volatile("svc 0");
}
```

When SVC is executed, the NVIC immediately stacks various registers including r0 and r1 and then executes the interrupt handler. The interrupt handler then needs to grab the values of r0 and r1 from the stack. The value in r0 is the function pointer while r1 is a pointer to some data in the caller's context. The r0 value is type casted as a function and executed with a single argument, the value of r1.

> **Super Important Note**. If you are using the SVC instruction in a context-switching environment, you must take care to ensure the SVC executes in the context in which it is called. You can have a situation where SysTick arrives in the clock cycle following the SVC instruction. SysTick has higher priority in this situation. So if SysTick forces a context change, the SVC handler will execute after the SysTick handler and will be looking at the wrong stack. That's bad. See [the Stratify OS context switcher for how to work around this](https://github.com/StratifyLabs/StratifyOS/blob/master/src/cortexm/task.c).

```c++
typedef void (*svcall_t)(void*);

void svcall_handler(void){
  register uint32_t * frame;
  register svcall_t call;
  register void * args;
  asm volatile ("MRS %0, psp\n\t" : "=r" (frame) ); //assumes PSP in use when service_call() invoked
  call = (svcall_t)frame[0];
  args = (void*)(frame[1]);
  call(args);
}
```

Finally, to make a privileged call:

```c++
void my_priv_func(void * data){
    int * my_int;
    my_int = data;
    if (*my_int == 10 ){
        *my_int = 0;
    }
}

int main(void){
    int var;
    var = 10;
    service_call(my_priv_func, &var); //executes my_priv_func() in interrupt mode
    if( var == 0 ){
        //if this is true, that means everything worked
    }
    return 0;
}
```

## Conclusion

This very simple code sample provides a powerful and effective mechanism for using the ARM Cortex-M SVCall (service call) interrupt. It allows an arbitrary function to be executed uninterrupted in privileged mode. The code is both re-entrant and thread-safe (as long as the context-switch can't interrupt the service call handler).
