---
categories:
- Device Tips
date: "2022-12-01"
layout: post
tags:
- cpp
- programming
- microcontroller
- cortex-m
- linux
title: Callbacks and Lambdas in Embedded C++
---

In [A simple approach to C++ callbacks]({{< ref "2019-08-05-Callbacks-in-Cpp.md" >}}), I talk about the simplest way to call instantiated C++ objects in callbacks. This approach works well when mixing C and C++, but it has type-safety problems that are not resolved at compile time. The simple version of type-safe callbacks in C++ involves using `std::function` and lambdas.

## Unsafe Types

Let's look again at how [simple C++ callbacks]({{< ref "2019-08-05-Callbacks-in-Cpp.md" >}}) work using `pthread_create` as an example.

```cpp
#include <pthread.h>

class MyClass {
public:
  static void * do_some_work_in_a_thread(void * context){
    // ------------------------- This is the type-unsafe operation we want to avoid
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

The `reinterpret_cast<MyClass*>(context)->do_some_work();` line requires that the call to `pthread_create()` passes a pointer to a `MyClass` object. As a programmer, you need to keep track of this. The compiler won't give you an error if you do it wrong. This is where `std::function` comes to the rescue.

## What is `std::function`

In the example object, we need two pointers:

- One to `MyClass::do_some_work_in_a_thread`
- One to `&working_class`

`std::function` combines these two pointers into a single object. Instead of a generic function pointer (`void*(*function)(void*)`) and a generic data pointer (`void*`), it uses C++ generic programming to have a specialized function pointer to a specialized data type. 

## What is a lambda?

A lambda in C++ is just a shortcut for creating a callable object.

For example:
```cpp
  int x = 0;
  auto add = [x](int a, int b) -> int { return x + a + b; }
```

Will generate:

```cpp
struct add {
   //capture values go here
   //because we are capturing by value, get a copy of x
   int x;

   add(int x) : x{x}{}
   int operator()(int a, int b){
        return x + a + b;
   }
};
```

The size of RAM required for a lambda is determined by the number of captured values. More captured values require more RAM. This is important because if the lambda is too large for `std::function`, the compiler will generate code to dynamically allocate memory when passing the lambda to `std::function`.

The good news is that you can capture the enclosing class and all of its members by capturing `this`.

```cpp
auto add = [this](int a, int b) -> int { return this->x + a + b; }
```

This gives us everything we need to safely capture context when using callbacks.

## Putting it together

Let's look at a class that wraps `pthread_create()` using a `std::function` (this is basically what `std::thread` does) rather than a C-style callback with a `void*` argument.

```cpp
#include <pthread.h>
struct Thread {
  //instead of taking a `void *` argument we
  using Function = std::function<void()>;
  //warning, this is a simplistic implementation
  //that ignores synchronization mechanisms and move semantics
  //See https://github.com/StratifyLabs/API/blob/v1.6/libraries/ThreadAPI/include/thread/Thread.hpp
  //for more details or just use std::thread
  void create(Function function){
    this->function = function;
    pthread_create(&thread, 
      nullptr, //use default thread attributes
      create_thread_function, 
      this);
  }
private:
  //we still have to use `reinterpret_cast` because we need to deal with the C-style API
  //but we only do this once instead of everytime we create a thread
  static void * create_thread_function(void * args){
    reinterpret_cast<Thread*>(args)->function();
    return nullptr;
  }
  Function function;
}
```

Now we just use the lambda to capture any context we want to send to the thread.

```cpp
  MyClass my_class;
  Thread thread;
  thread.create([&my_class](){
    //we capture a reference to my_class, so we can use it as if it is in scope
    my_class.do_some_work();
    printf("Running in a thread: copy of x is %d\n", x);
  });
```

If we try to capture too much information (ie, the size of the lambda exceeds what `std::function` can hold), `std::function` will dynamically allocate the manage the extra space.

```cpp
  char array[128];
  Thread thread;
  thread.create([array](){ //std::function will need to allocate space for array since it is copied (captured by value)
    //use a copy of array in the thread
  });
```

## Performance and Code Size Penalty

If you keep the capture size small enough to fit in `std::function`, using this approach has a similar cost to using C-style callbacks with the dangerous `reinterpret_cast`. To see an example, go to [compiler explorer](https://godbolt.org/z/Y9nE43dPK) and change the value of USE_FUNCTION to switch between std::function and a C-style function plus void*.

```cpp
#include <functional>

#define USE_FUNCTION 1

struct Foo {
    int value{};
    void update_value(int updated) noexcept {
        value = updated;
    }

    using Callback = void(*)(void*,int);
    static void update_value_callback(void * context, int value){
        reinterpret_cast<Foo*>(context)->update_value(value);
    }

    using CallbackFunction = std::function<void(int)>;
};

#if USE_FUNCTION
void update_value(int value, Foo::CallbackFunction callback_function){
    callback_function(value);
}
#else
void update_value(int value, void * context, Foo::Callback callback){
    callback(context, value);
}
#endif

int main(int argc, char * argv[]) {
    Foo foo;
    #if USE_FUNCTION
        update_value(argc, [&foo](int value){
        foo.update_value(value);
    });
    #else
    update_value(argc, &foo, Foo::update_value_callback);
    #endif
    printf("value is %d\n", foo.value);
}
```

### Results

#### C-Style Function Pointer

23 total instruction lines.

```
update_value(int, void*, void (*)(void*, int)):
        cmp     r1, #0
        mov     r3, r1
        cmpne   r2, #0
        beq     .LBB0_2
        mov     r1, r0
        mov     r0, r3
        bx      r2
.LBB0_2:
        push    {r11, lr}
        bl      std::__throw_bad_function_call()
main:
        push    {r11, lr}
        mov     r1, r0
        ldr     r0, .LCPI1_0
        bl      printf
        mov     r0, #0
        pop     {r11, lr}
        bx      lr
.LCPI1_0:
        .long   .L.str
.L.str:
        .asciz  "value is %d\n"
```

#### `std::function`

30 total instruction lines.

```
update_value(int, std::function<void (int)>):
        push    {r11, lr}
        sub     sp, sp, #8
        str     r0, [sp, #4]
        ldr     r0, [r1, #8]
        cmp     r0, #0
        beq     .LBB0_2
        ldr     r3, [r1, #12]
        mov     r2, r1
        add     r1, sp, #4
        mov     r0, r2
        mov     lr, pc
        bx      r3
        add     sp, sp, #8
        pop     {r11, lr}
        bx      lr
.LBB0_2:
        bl      std::__throw_bad_function_call()
main:
        push    {r11, lr}
        mov     r1, r0
        ldr     r0, .LCPI1_0
        bl      printf
        mov     r0, #0
        pop     {r11, lr}
        bx      lr
.LCPI1_0:
        .long   .L.str
.L.str:
        .asciz  "value is %d\n"
```

The `std::function` generates slightly more assembly but offers compile-time type safety.

## Conclusion

Mixing C-style callbacks with C++ can cause type-safety issues and difficult-to-debug runtime errors. Using `std::function` and lambdas can maintain type-safety and similar performance and code size. However, when mixing C and C++, `reinterpret_cast` may be necessary.

