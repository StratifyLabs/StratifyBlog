---
categories:
- Device Tips
date: "2022-01-27"
layout: post
tags:
- cpp
- programming
- microcontroller
- cortex-m
title: Multi-threaded Singleton Access in embedded C++
---


[Earlier I described how I use a Meyer's Singleton to manage C++ objects that are tied to the hardware]({{< relref "2021-11-29-Using-Singletons-in-embedded-cpp.md" >}}). In this post, I want to share how I access singleton's in a multi-threaded environment.

If you have ever tried to debug multi-threaded programs that read/write the same data you know it is difficult to debug problems. The problems show up intermittently and sometimes only very rarely. In C++, you can avoid these problems by scoping access to shared data and aggressively asserting if you forgot to lock a mutex.

## Scoped Singleton Access

We want to make the compiler do as much of the work as possible, so the first step is to create a class that locks the singleton on construction and unlocks it on destruction. Below `IO` is the class we want to access from multiple threads. The nested class `IO::Scope` allows us to use a mutex
whenever `IO` is accessed. 

> Keep in mind, you don't have to use a nested class to use this technique.

```cpp
struct IO {

  class Scope {
    public:
    Scope(){
      auto & io = IO::instance();
      //This will block if another thread is already accessing IO
      io.scope_mutex.lock();

      //use a recursive mutex allows the user to next Scope instances
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

  Uart uart;

private:
  IO() : uart("/dev/uart0"){}

  //This will allow Scope and IoAccess
  //to use instance() but nothing else
  friend Scope;
  friend class IoAccess;

  //mutex should be recursive so the same thread can
  //lock it recursively without worrying about errors
  Mutex scope_mutex = Mutex(Mutex::Attributes().set_type(Mutex::Type::recursive));
  pthread_t scope_pthread = {};
  int scope_lock_count = 0;

  static IO & instance(){
    //constructed on first access
    static IO m_instance;
    return m_instance;
  }

};
```

## Accessor Class

Now I create a class that allows me to easily access the members of my singleton. This class ensures that whenever I access a member of `IO`, I do so with the `IO::Scope` locked to the current thread.

```cpp
struct IOAccess {
  static IO & io(){
    auto & io = IO::instance();
    //this guarantees the caller has the scope locked
    API_ASSERT(io.scope_pthread == Thread::self());
    return IO::instance();
  }

  static Uart& uart(){ return io().uart; }
};
```

## Example using Accessor

Now let's take a look at what it looks like to use `IOAccess` in an inherited class called `Logic`.

```cpp

class Logic: public IOAccess {
public:
  void do_some_work(){
    {
      IO::Scope io_scope;
      //anywhere in this scope I can access io()
      uart().write("Hello World\n");
    }

    //this access to io() will cause the program to
    //halt using an ASSERT
    uart().write("Failed\n");

    //This line won't compile at all because IO::instance() is private
    //and Logic is not a friend class
    IO::instance().uart.write("Won't compile\n");
  }

  void do_some_other_work(){
    IO::Scope io_scope;
    io().uart.write("doing some other work\n");

    //this can be called because Scope uses a recursive mutex
    do_some_work(); 
  }

}
```

## Last Thing

Multi-threaded data access bugs can be very difficult to find and fix. Using C++, you can largely automate the process of locking and unlocking data using RAII. You lock the data in the constructor (`IO::Scope`) and unlock it in the destructor. If you create a `friend class` dedicated to accessing the shared data, you can have that `friend class` check for a mutex lock and only allow the program to proceed if all is well. This puts most of the work of ensuring access to shared data is mutually exclusive on the compiler. Using `assert()`, you will know immediately if you forgot to scope the access rather than waiting for the bug to manifest itself in strange ways.