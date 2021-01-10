---
categories:
- Device Tips
date: "2020-12-22"
layout: post
tags:
- microcontroller
- cpp
- programming
title: RAII Everywhere in Embedded C++ 
---

I recently rewrote a family of C++ libraries that I had been using for 10 years. The main themes of the rewrite include:

- [Thread Local Error Contexts]({{< relref "2020-12-14-Thread-Local-Error-Context-in-Cpp.md" >}})
- [Method Chaining]({{< relref "2020-12-15-Method-Chaining-in-Cpp.md" >}})
- [Strong Arguments]({{< relref "2019-08-16-Using-Strong-Type-Arguments-on-Embedded-Cpp.md">}})
- **RAII Everywhere**
- [Filesystem Inspired Abstraction]({{< relref "2020-12-29-Filesystem-Inspired-Abstraction-in-embedded-cpp.md" >}})


# What is RAII?

RAII stands for "Resource Acquisition is Initialization". It's a really terrible name for an amazing concept. Unfortunately, the name leaves out the best part: it also handles finalization.

The basic idea of RAII is that the code to initialize something is always in the C++ constructor and finalization is always in the destructor. It can be useful for:

- Managing System Resources
- Guarding Resources
- Structured Output

## RAII for Managing System Resources

One common usage is managing system resources. In the class below, data is allocated in the constructor and freed in the destructor.

```c++
class MyData {
public:
  MyData(size_t size){
    m_data = malloc(size);
  }

  ~MyData(){
    if( m_data != nullptr ){
      free(m_data);
    }
  }

private:
  void * m_data = nullptr;
}
```

### Simple Enough?

The sample above seems simple enough but it has some behaviors that will cause problems. Such as, what happens when a `MyData` object is copied using the built-in copy operators? The answer is: big problems will happen. To get around this, we can prohibit the compiler from copying objects and allow it to move them.

```c++
//delete built-in copy
MyData(const MyData & a) = delete;
MyData& operator=(const MyData & a) = delete;

//implement built-in move
MyData(MyData && a){
  std::swap(m_data, a.m_data);
};
MyData& operator=(MyData && a){
  std::swap(m_data, a.m_data);
  return *this;
}
```

Now, the compiler will never copy `MyData`. In some cases, you may need to add `std::move()` to get things to compile the way you want. The final result is that allocated pointers are freed exactly once.

Sometimes you might want to have a `MyData` object to be used as a placeholder and not hold any memory. For that we can add:

```c++
//add default constructor
MyData(){} //m_data will be nullptr

//later we can do this
MyData data; //don't know the size now

//sometime later
data = std::move(MyData(1024));
```

This technique is very helpful when maximizing the usage of RAII.

### Other System Resources

Dynamic memory is just one example, you can also use RAII with:

- Files
- Sockets
- Devices
- Threads


## Guarding Resources

Another common use of RAII is when locking/unlocking Mutexes. This is a "guard" approach that works for other situations as well.

Here's the mutex guard:

```c++
class Guard {
public:
  explicit Guard(Mutex &mutex) : m_mutex(mutex) { mutex.lock(); }
  ~Guard() {
    m_mutex.unlock();
  }

private:
  Mutex &m_mutex;
};
```

The usage is then:

```c++
Mutex mutex;
{
  Guard guard(mutex); //locks
  //use the resource
} //unlock when guard goes out of scope
```

Other guards can be used to restore the state of something. Consider this example:

```c++
class Worker {
  int flags(){
    return m_flags;
  }

  void set_flags(int value){
    m_flags = value;
  }

  class FlagGuard {
  public:
    FlagGuard(Worker & worker) : m_worker(worker){
      m_value = worker.flags();
    }
    ~FlagGuard(){
      worker.set_flags(m_value);
    }
  private:
    Worker & m_worker;
    int m_value;
  }

private:
  int m_flags;
}

Worker my_worker;
my_worker.set_flags(1<<1);

{
  //in this block I want to change
  //flags but only temporarily
  Worker::FlagGuard guard(my_worker);
  my_worker.set_flags(1<<2);
  //do work with modified flags

} //flags are restore
```

Guards are especially helpful in functions that have multiple exit points.

```c++
void do_some_work(Worker & worker){
  Worker::FlagGuard flag_guard(worker);

  if( check_something ){
    return; //guard deconstructs
  }

  if( check_something_else ){
    return; //guard deconstructs
  }

  all_checks_passed();
} //guard deconstructs
```

There isn't a definitive list of places you can use RAII to guard resources, so you need to keep a keen eye out for them.

## Structured Output

Using RAII with structured output is very similar to the guard example. It is more of an example of something to keep an eye out for rather than a category in and of itself.

I use RAII when printing structured output such as JSON or YAML or markdown. The code is available in the [PrinterAPI framework](https://github.com/StratifyLabs/API). Here are a few usage examples:

```c++
#include <printer.hpp>

JsonPrinter json_printer;
{
  Printer::Object object(json_printer, "name");
  json_printer.key("first", first_name);
  json_printer.key("last", last_name);
} //closes JSON object

MarkdownPrinter markdown_printer;
{
  Markdown::Code code(markdown_printer, "c++");
  markdown_printer << "printf(\"Hello World\");";
} //prints closing ticks
```

## What to do now?

Using RAII is a great way to let the compiler do the work for you. It will automatically "remember" to free memory, close files, unlock mutexes, join threads, restore states, and format output. You just have to keep an eye out for when you see these initialize/finalize constructs emerging in your code and stick that code in constructors/deconstructors.
