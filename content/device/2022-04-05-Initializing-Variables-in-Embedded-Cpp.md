---
categories:
- Device Tips
date: "2022-04-05"
layout: post
katex: true
chart: true
tags:
- cpp
- programming
- microcontroller
- cortex-m
title: Initializing Variables in Embedded C++
---

C++ is infamous for the number of ways you can initialize a variable. If you are working with large distinct codebases (aka, you are a software engineer), you need to know them all. However, if you are an embedded C engineer looking to improve your C++ skills, you just need to know the few ways that work best for you.

## Variable Initialization Options

When a local variable is declared, you have several ways of initializing it.

```cpp
//default initialization
int variable;
//Copy/move assignment initialization
int variable = 10;
//direct initialization using parenthesis (aka round brackets)
int variable(10); 
//brace (aka curly brackets) initialization
int variable{10}; //direct brace
int variable = {6}; //copy brace
int variable{}; //value initialization
int variable = {}; //value initialization
```

**MyClass and MyStruct**

We are going to use this simple class below as we talk about each of these.

```cpp

class MyClass {
public:
  MyClass = default;
  explicit MyClass(int value) : x{value} {}
  MyClass(int x_value, int y_value) : x{x_value}, y{y_value} {}
private:
  int x{};
  int y{};
}

struct MyStruct {
  int x{};
  int y{};
}
```

> In C++, the only difference between `struct` and `class` is that `class` members are `private` by default and `struct` members are `public` by default.

**Default Initialization**

For a fundamental type (like `int`, `float`, `void*`), default initialization on the stack leaves the value in an indeterminate state. You shouldn't ever do this. Consider this example:

```
int variable;
read(fd, &variable, sizeof(int));

//this is better
//create a custom function to assign the variable
//we can use const so that we know this variable isn't
//modified later
const int variable = read_variable_from_file(fd);
```

For a `class` or `struct`, the default constructor is called. In the case of `MyClass`, the default constructor uses value initialization with the empty braces which assign the value of `x` and `y` to zero. The same is true for `MyStruct`.

We will discuss below how to ensure all members of a `class` or `struct` are appropriately initialized during construction.

**Copy Initialization**

For fundamental types, the value assigned is copied to the variable. For `class` and `struct` types, the value is either copied or moved depending on the `class` or `struct`. If the assigned value is an `rvalue`, the value is moved. If it is an `lvalue`, the value is copied.

> An `lvalue` is a value that has a location in memory. That is you can use a pointer/reference with it. An `rvalue` is defined as not being an `lvalue`. It is a temporary value that may only exist in a register.

```cpp
//Move the temporary MyClass() to my_class
MyClass my_class = MyClass();
//copy my_class to my_class2
MyClass my_class2 = my_class;
```

Not everything in C++ is movable. If a `class` cannot be moved, it is copied. The compiler will make this decision without any warnings or errors.

**Direct Initialization**

```cpp
int variable(10);
MyClass my_class(5,10);
MyStruct my_struct(5,10); //this is a compiler error
```

For fundamental types, direct initialization is the same as copy initialization. For `MyClass`, the constructor taking two integers as arguments is used `MyClass(int x_value, int y_value)`. The compiler searches all the constructors for the one that matches the call site. `MyStruct` does not provide a constructor that takes two arguments so the compilation of that line will fail.

**Brace Initialization**

Brace initialization is also known as uniform initialization because it can be used everywhere where direct initialization is limited to matching constructors.

For fundamental types, brace initialization offers two advantages over other methods. Brace initialization disallows narrowing types. For example:

```cpp
int w = 4.5f; //implicit convertion from float to int
int w{4.5f}; //compilation error
```

Brace initialization also allows easy access to value initialization.

```cpp
int variable{};
int variable = {};
```

For fundamental types, value initialization assigns the values to zero. This also works for plain old data structures (c-style structs).

```cpp
typedef struct {
  int x;
  int y;
  int z;
} c_style_t;

//this will assign all values to zero
c_style_t c_style = {};

//WAY better than writing this
//but it many cases this is exactly what the
//compiler does
memset(&c_style, 0, sizeof(c_style));
```

Brace initialization on `class` or `struct` will either match the constructor or it will match the list in the `{}` with `public` members of the `struct` or `class`. If not all members are `public`, a constructor must match.

```cpp
//uses constructor
MyClass my_class{5,10};
//uses constructor then move semantics
MyClass my_class = {5,10};

MyStruct my_struct{5,10};
MyStruct my_struct = {5,10};
```

Another nice feature of brace initialization is you can include the member names to make the code clear. This is the same syntax as `C` but the members **must** be in the same order they are declared or the compilation will fail.

```cpp
MyStruct my_struct{.x = 5, .y = 10};
MyStruct my_struct = {.x = 5, .y = 10};
MyStruct my_struct = {.y = 5, .x = 10}; //compilation error

//this doesn't work with private members
MyClass my_class{.x = 5, .y = 10}; //compilation error
```

## Member Variable Initialization

Member variables within classes have a few things to consider in addition to local variable initialization.

First, variables are initialized (including calling constructors) in the order they are declared. So `x` is assigned zero before `y`. The constructor of the enclosing class is called last. Order isn't too important in `MyClass` but in some cases, it matters. When a `class` is destructed, the destructors are called in the exact opposite order as the constructors.

In `MyClass` and `MyStruct`, we used value initialization to assign the variables `x` and `y`. We could have used any of the initialization types. In `MyClass`, we also use a member constructor list with `MyClass(int x_value, int y_value)`. The important thing to understand is that if a variable appears in a member constructor list, the declaration initialization will not be called.

```cpp
//x is only inititlized with x_value, x{} is not executed
//y is initialized from int y{}
explicit MyClass(int value) : x{value} {
  y = 10; //both y{} and y = 10 are executed -- don't do this
  //just put , y{10} after x{value} above
}
//neither x{} nor y{} is executed
MyClass(int x_value, int y_value) : x{x_value}, y{y_value} {}
```

## The End

There are a bunch of ways to initialize variables in C++. In most cases, the differences are a matter of style. But remember, brace initialization has a couple of advantages. The typing is more strict for fundamental types. It also allows easy access to value initialization that is easier to code than `memset()`. I prefer parenthesis for classes (just so I know I am working with a `class`) and braces everywhere else.


