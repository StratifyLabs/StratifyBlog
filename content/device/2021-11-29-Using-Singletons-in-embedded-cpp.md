---
categories:
- Device Tips
date: "2021-11-29"
layout: post
draft: false
tags:
- cpp
- programming
- microcontroller
- cortex-m
title: Using Singletons in Embedded C++
---

Embedded C++ ([however you define it]({{< relref "2020-12-23-What-is-Embedded-Cpp.md" >}})) is a great tool for developing firmware for microcontrollers. C++ supports object-oriented programming which is great for solving complex problems by deconstructing them into simple objects. Some of these objects are pure software constructs and can be created and destroyed willy-nilly. Other objects are mapped to real-world hardware. Using a singleton is a great way to manage this mapping.


## Uart Class

Let's start with a class that allows access to a UART.

```c++

#include <thread/Mutex.hpp>

class Uart {
public:
  Uart(const char * path){
    m_fd = open(path, O_RDWR);
  }

  ~Uart(){
    if( m_fd >= 0 ){
      close(m_fd);
    }
  }


  void write(const char * message){
    thread::Mutex::Scope mutex_scope(m_mutex);
    ::write(m_fd, message, strlen(message));
  }

private:
  thread::Mutex m_mutex;
  int m_fd = -1;
}
```

## Using a `static` Member Variable

Now, let's assume the system has exactly one UART that is accessed at `/dev/uart0`. In software, we want to create exactly one instance of the `Uart` object. One way of making this happen is using a `static` member variable.


In `IO.hpp`, we have:

```c++
struct IO {
  static Uart uart;
}
```

Then in `IO.cpp`, we need to have:

```c++
static Uart IO::uart("/dev/uart0");
```

There are two things I don't like about this approach:

1. I need to declare (hpp) and define (cpp) the `Uart` object in different places. This isn't so bad with one member. With 20 to 40 hardware mappings, it's tedious.
2. I don't have precise control over when the `Uart` object is constructed. The `Uart` object will be constructed before `main()` is called. If I have other `static` members in different `cpp` files, I don't have control of the order of construction. What if some other part of the system needs to be initialized before the `Uart` can be constructed?

I do like that this statically allocates the `Uart` on the heap, but Scott Meyer (he has written some [books](https://www.amazon.com/Effective-Modern-Specific-Ways-Improve/dp/1491903996)) gave us a clever way to improve on this. This is called Meyer's singleton.

## The Meyer's Singleton

```c++

struct IO {
  static IO & instance(){
    //constructed on first access
    static IO m_instance;
    return m_instance;
  }

  //constructed in order on first access
  Uart uart;
  I2C i2c;
  Spi spi;

private:
  //don't allow any other class to construct
  IO() : uart("/dev/uart0"){}

};
```

Meyer's singleton fixes both of my gripes with a simple `static` member variable.

1. The `Uart` member variable does not have to be declared in the `cpp` file. The `instance()` member can optionally be in both the `hpp` and `cpp` files.
2. I have precise control over when the entire singleton is constructed. It will be constructed on the first call to `IO::instance()`.

And, of course, the singleton is statically allocated on the heap.

To guarantee `Uart` is a singleton, we want to make the `Uart` constructor private and add `IO` as a `friend`.

```c++
class Uart {
public:
  //remove publich constructor
  ...
private:
  friend class IO;
  Uart(const char * path){
    m_fd = open(path, O_RDWR);
  }
  ...
}
```

### Access Class

I like to add an access class that can be inherited by classes that need access to the singleton.

```c++
struct IOAccess {
  static Uart& uart(){ return IO::instance().uart; }
  static I2C& i2c(){ return IO::instance().i2c; }
  static Spi& spi(){ return IO::instance().spi; }
};

class MyObject: public IOAccess{
  ...
  void send_message(const char * message){
    uart().write(message);
  }
};

class MyOtherObject: public IOAccess {
  ...
  void send_notification(const char * notification){
    uart().write(notification);
  }
};
```

## That's It

Singletons are a great way to map finite hardware resources to C++ objects. Using a simple `static` member variable is tedious to code and impossible to precisely initialize. Meyer's singleton fixes the problems with a simple `static` member. It is a great construct to know for embedded C++ development.


