---
categories:
- Device Tips
date: "2022-01-27"
layout: post
draft: false
tags:
- cpp
- programming
- microcontroller
- cortex-m
title: Multi-threaded Singleton Access in embedded C++
---

[Earlier I described how I use a Meyer's Singleton to manage C++ objects that are tied to the hardware]({{< relref "2021-11-29-Using-Singletons-in-embedded-cpp.md" >}}). In this post, I want to share how I access singleton's in a multi-threaded environment.

We want to make the compiler do as much of the work as possible, so the first step is to create a class that locks the singleton on construction and unlocks it on destruction.

```c++
struct IO {

  class Scope {
    public:
    Scope(){
      auto & io = IO::instance();
      io.scope_mutex.lock();
      if( io.scope_lock_count == 0 ){
        io.scope_pthread = Thread::self();
        ++io.scope_lock_count;
      }


    }
    ~Scope(){
      auto & io = IO::instance();
      --io.scope_lock_count;
      if( io.scope_lock_count == 0 ){
        io.scope_pthread = {};
      }
      io.scope_mutex.unlock();
      
    }
  };

  //constructed in order on first access
  Uart uart;

private:
  //don't allow any other class to construct
  IO() : uart("/dev/uart0"){}

  friend Scope;
  friend class IoAccess;
  //mutex should be recursive so the same thread can
  //lock it recursively without worrying about errors
  Mutex scope_mutex = Mutex(Mutex::Attributes().set_recursive());
  pthread_t scope_pthread = {};
  int scope_lock_count = 0;

  static IO & instance(){
    //constructed on first access
    static IO m_instance;
    return m_instance;
  }

};
```

```c++
struct IOAccess {
  static IO & io(){
    auto & io = IO::instance();
    //this guarantees the caller has the scope locked
    API_ASSERT(io.scope_pthread == Thread::self());
    return IO::instance();
  }

  static Uart& uart(){ return io().uart; }
  static I2C& i2c(){ return io().i2c; }
  static Spi& spi(){ return io().spi; }
};
```


```c++
{
  IO::Scope io_scope;
  //anywhere in this scope I can access io()
  io().uart.write("Hello World\n");
}

//this access to io() will cause the program to
//halt using an ASSERT
io().uart.write("Failed\n");

//This line won't compile at all because IO::instance() is private
IO::instance().uart.write("Won't compile\n");
```
