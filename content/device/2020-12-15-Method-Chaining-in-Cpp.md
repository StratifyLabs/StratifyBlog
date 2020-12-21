---
categories:
- Device Tips
date: "2020-12-15"
layout: post
tags:
- microcontroller
- cpp
- programming
title: Concise Code Using Method Chaining in C++ 
---

I recently rewrote a family of C++ libraries that I had bee using for 10 years. The main themes of the rewrite include:

- [Thread Local Error Contexts]({{< relref "2020-12-14-Thread-Local-Error-Context-in-Cpp.md" >}})
- **Method Chaining**
- [Strong Arguments]({{< relref "2019-08-16-Using-Strong-Type-Arguments-on-Embedded-Cpp.md">}})
- RAII Everywhere
- Filesystems Inspired Abstraction

# Method Chaining

- Managing object lifetime
- Writing clear and concise code

# What is Method Chaining

Method chaining in C++ is when a method returns a reference to the owning object so that another method can be called.

```c++
class Object {
public:
  Object & set_value(int a){
    m_value = a;
    return *this;
  }

  Object & set_name(const char * a){
    m_name = value;
    return *this;
  }

private:
  int m_value = 0;
  const char * m_name = nullptr;
}

int do_something(const Object & object);
```

Here are two examples of calling `do_something()` with and without method chaining.

`do_something()` without method chaining:

```c++
Object object;
object.set_name("object");
object.set_value(100);
do_something(object);
```

`do_something()` **with** method chaining:

```c++
do_something(Object().set_name("object").set_value(100));
```

The first thing to notice is how much more concise the method chaining case is. What is more subtle is the lifetime of the `Object()`. Without method chaining, `object` lives as an `lvalue` until it goes out of scope. You could add `{}` to expedite that process. But that would also make the code even less concise.

With method chaining, the lifetime of `Object()` is naturally minimized allowing any resources used by object to be released as soon as `do_something()` finishes.

# Using Macros

It can get quite tedious to write setters for classes that support method chaining. I get around this by creating macros for both getters and setters allowing class member variables to be declared in a single line of code.

This macro declares a `bool` member:

```c++
#define API_ACCESS_FUNDAMENTAL(c, t, v, iv)                                    \
public:                                                                        \
  t v() const { return m_##v; }                                                \
  c &set_##v(t value) {                                                        \
    m_##v = value;                                                             \
    return *this;                                                              \
  }                                                                            \
                                                                               \
private:                                                                       \
  t m_##v = iv
```

Object then becomes:

```c++
class Object {
  API_ACCESS_FUNDAMENTAL(Object,int,value,0);
  API_ACCESS_FUNDAMENTAL(Object,const char *,name,nullptr);
}
```

This has the added bonus of forcing you to provide an initial value to all members which has saved me debugging time on more than one occasion.

# So What?

Method chaining all by itself can help manage object lifetime while enabling clear and concise code, but its true power is shown when combined with other techniques like strong arguments and filesystems like abstraction. You can check out more examples and see the full source code on [Github](https://github.com/StratifyLabs/API).

