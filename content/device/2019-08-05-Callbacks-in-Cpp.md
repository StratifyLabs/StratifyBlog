---
categories:
- Device Tips
date: "2019-08-05"
layout: post
tags:
- microcontroller
- programming
- cpp
title: A Simple Approach to C++ Callbacks
katex: true
wave: true
msc: true
---

> See how to create safer callbacks when using just C++ in [Callbacks and Lambdas]({{< ref "2022-12-01-Callback-and-Lambdas-in-embedded-cpp.md" >}}).

Callbacks in C++ can be quite tricky compared to C. In C, you simply pass a function pointer, then call it like a normal function whenever you like. The object-oriented nature of C++ doesn't allow such a simple approach. It is easy to pass `static` methods as callbacks because they are very similar to C functions. But `static` methods don't allow access to non-static members of the class. This can be a bit of a pain. I have settled on an approach to C++ callbacks that makes them almost as simple as their C counterparts.

With every callback, one of the arguments is provided by the caller and passed to the callback. For C++ classes, the argument is a pointer to the object that should execute the callback.

The following is an example using some simple math callbacks. 

```cpp
//this takes a callback argument, 
//context needs to be provided by the caller and passed to the callback
int do_math(
  void * context, 
  int a, 
  int b, 
  int (*operation)(void * context, int a, int b)
  ){
  return operation(context,a,b);
}

class MyClass {
public:
  static int add_callback(void * context, int a, int b){
    //no access to member variables here
    return reinterpret_cast<MyClass*>(context)->add(a,b);
  }

  static int subtract_callback(void * context, int a, int b){
    //no access to member variables here
    return reinterpret_cast<MyClass*>(context)->subtract(a,b);
  }

  int add(int a, int b){
    //access to member variables is OK here
    return a + b + m_offset;
  }

  int subtract(int a, int b){
    //access to member variables is OK here
    return a - b + m_offset;
  }

   int m_offset;
}

int main(int argc, char * argv[]){
  MyClass no_offset;
  no_offset.m_offset = 0;

  MyClass offset;
  offset.m_offset = 10;

    //the compiler won't allow passing MyClass::add() directly because it is non-static
  do_math(&no_offset, 
    5, 
    5, 
    MyClass::add_callback); //returns 5+5 + 0
  do_math(&offset, 
    5, 
    5, 
    MyClass::subtract_callback); //return 5-5 + 10

  return 0;
}
```

Creating threads is a great use-case for this approach as well. POSIX threads need a callback and an arbitrary argument (just what we need for this approach to work).

```cpp
int pthread_create(pthread_t * thread,
  const pthread_attr_t * attr,
  void *(*start_routine)(void *),
  void *arg);
```

The `start_routine` is a callback, and `arg` is an arbitrary value that gets passed to `start_routine`. The following class shows how a method can be executed in the current thread or a new one.

```cpp
#include <pthread.h>

class MyClass {
public:
  static void * do_some_work_in_a_thread(void * context){
    return reinterpret_cast<MyClass*>(context)->do_some_work();
  }

  void * do_some_work(){
    //this can be run in a thread or the current context.
    return nullptr;
  }
}

int main(int argc, char * argv[]){
  MyClass working_class;
  pthread_t thread;
  working_class.do_some_work(); //execute in current thread
  pthread_create(&thread, 
    nullptr, //use default thread attributes
    MyClass::do_some_work_in_a_thread, 
    &working_class);
  //working_class.do_some_work() will be executing in a new thread

  //... wait for thread to complete
  return 0;
}
```

I hope this technique is as useful for you as it has been for me!

