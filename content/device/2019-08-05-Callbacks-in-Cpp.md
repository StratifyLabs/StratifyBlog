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

> To create safer callbacks in C++, see [Callbacks and Lambdas]({{< ref "2022-12-01-Callback-and-Lambdas-in-embedded-cpp.md" >}}).

C++ callbacks can be more complex than those in C. In C, you pass a function pointer and call it like a normal function. In C++, this is not possible due to its object-oriented nature. It is easy to pass `static` methods as callbacks because they are similar to C functions, but `static` methods cannot access non-static class members. I have settled on an approach that simplifies C++ callbacks and makes them similar to C counterparts. The caller provides an argument to the callback, which is a pointer to the object that should execute the callback.

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

Creating threads is a good use case for this approach. POSIX threads require a callback and an arbitrary argument, which this approach provides.

```cpp
int pthread_create(pthread_t * thread,
  const pthread_attr_t * attr,
  void *(*start_routine)(void *),
  void *arg);
```

The `start_routine` is a callback and `arg` is a value passed to `start_routine`. The following class shows how a member function can be executed in the current thread or a new one.

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

I hope this technique is useful for you too!
