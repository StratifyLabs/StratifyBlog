---
categories:
- Device Tips
date: "2022-12-14"
layout: post
tags:
- cpp
- programming
- microcontroller
- cortex-m
- linux
title: From C++ to Rust
---

I recently started learning Rust. As a long-time C++ programmer, I wanted to understand how the C++ concepts I have applied for years worked in Rust. This is a side-by-side comparison of how many programming concepts and implementation details of C++ are accomplished in Rust.

I rate each aspect of the language as:

- :bangbang: Same: same conceptually with minor execution/syntax differences
- :interrobang: Similar: similar conceptually with execution differences
- :x: Different: somewhat similar conceptually with major execution differences

> This is not an exhaustive comparison.

- [Variables](#variables)
  - [:bangbang: Primitive Types](#bangbang-primitive-types)
    - [:bangbang: Literals](#bangbang-literals)
    - [:x: User Defined Literals](#x-user-defined-literals)
  - [:interrobang: Compound Data Types](#interrobang-compound-data-types)
    - [:x: Enums](#x-enums)
    - [:interrobang: Arrays](#interrobang-arrays)
    - [:interrobang: Tuples and Structs](#interrobang-tuples-and-structs)
  - [:bangbang: Constness and Mutability](#bangbang-constness-and-mutability)
  - [:bangbang: `auto` vs. inferred](#bangbang-auto-vs-inferred)
  - [:bangbang: Casting](#bangbang-casting)
  - [:x: Copy and Move Semantics](#x-copy-and-move-semantics)
    - [:interrobang: Copying and Moving Primitive Types](#interrobang-copying-and-moving-primitive-types)
    - [:x: Copying and Moving Compound Types](#x-copying-and-moving-compound-types)
    - [:x: References vs Borrowing](#x-references-vs-borrowing)
- [:interrobang: Procedural Programming](#interrobang-procedural-programming)
  - [:interrobang: Flow Control](#interrobang-flow-control)
    - [:interrobang: If/Else](#interrobang-ifelse)
    - [:x: switch vs match](#x-switch-vs-match)
      - [:bangbang: While Loops](#bangbang-while-loops)
    - [:bangbang: Ternary Operator](#bangbang-ternary-operator)
  - [Functions](#functions)
    - [Default Arguments](#default-arguments)
    - [Function Signatures](#function-signatures)
  - [Lamba's](#lambas)
    - [Immediately called Lambda](#immediately-called-lambda)
- [Object Orient Programming](#object-orient-programming)
- [Classes and Structs](#classes-and-structs)
  - [Constructors](#constructors)
- [Destructors](#destructors)
- [Polymorphism](#polymorphism)
- [Template Meta-Programming](#template-meta-programming)
  - [](#)
- [Standard Library](#standard-library)

Notes
- #includes and modules and crates
- Rust case
  - UpperCamelCase: types
  - snake_case: variables/functions


## Variables



Rust specifies `snake_case` for variable (and function) names. Compiler will warn if you don't use it. C++ does not specify a case.

### :bangbang: Primitive Types

| C++ | Rust | Size (C++/Rust) | 
|-----|------|------|
| `size_t` | `isize` | cpu specific |
| `ssize_t` | `usize` | cpu specific |
| `uint8_t` | `u8` | 8-bit |
| `int8_t` | `i8` | 8-bit |
| `uint16_t` | `u16` | 16-bit |
| `int16_t` | `i16` | 16-bit |
| `uint32_t` | `u32` | 32-bit |
| `int32_t` | `i32` | 32-bit |
| `uint64_t` | `u64` | 64-bit |
| `int64_t` | `i64` | 64-bit |
| `uint128_t` | `u128` | 128-bit |
| `int128_t` | `i128` | 128-bit |
| `const char *` | `&str` | varies |
| `float` | `f32` | 32-bit |
| `double` | `f64` | 64-bit |
| `bool` | `bool` | varies/1 Byte |
| `char` | `char` | 8-bit/32-bit |

#### :bangbang: Literals

| C++ | Rust | Type | 
|-----|------|------|
| `5'000` | `5_000` | Decimal |
| `0xff` | `0xff` | Hexadecimal |
| `O777` | `0o777` | Octal |
| `0b1111'0000` | `0b1111_0000` | Binary |
| `'A'` | `'A'` | Byte |
| `1.0f` | `1.0_f32` | floating point (single-precision) |
| `1.0` | `1.0` | floating point (double-precision) |

With Rust you can suffix a literal with a underscore-primitive type:

```rust
//rust
let x = 5.0_f32;
```

#### :x: User Defined Literals

```c++
//c++
auto operator"" _milliseconds(unsigned long long int value) -> long long int {
  return value;
}

//
const auto value = 5_milliseconds;
```

In Rust, you can have user-defined literals only within macros. Doing so is non-trivial.

### :interrobang: Compound Data Types

#### :x: Enums

```c++
//c++
enum class Color {
  red, green, blue
};
```

```rust
//rust
enum Color {
  Red, Green, Blue
}
```

An `enum` in Rust can also be used similar to a `std::variant`.

```rust
//rust
enum Option {
  None,
  Some(i32)
}

//usage
let option = Option::Some(5);
let nothing = Option::None;
```

#### :interrobang: Arrays

```c++
//c++
char x[5]; //may be uninitialized

//access
const auto z = x[0] + x[4]; //first and last
const auto ub = x[5]; //undefined behavior
```

```rust
//rust
let x: [i32; 5] = [1,2,3,4,5]; //must be initialized before 1st use
let y = [3; 5]; //y is [3,3,3,3,3];

//access
let z = x[0] + x[4];
let ub = x[5]; //won't compile if a dynamic value is used will panic at runtime
```

#### :interrobang: Tuples and Structs

In Rust, tuples are a native type. In C++, they are part of `std::tuple`.


### :bangbang: Constness and Mutability

C++ variables are mutable by default while Rust variables are constant by default.

```c++
//c++
const int x = 5;
int y = 10;
```

```rust
//rust
let x: i32 = 5;
let mut y: i32 = 10;
```

In Rust, variables are dropped after last use. This means a variable can be re-declared in the same scope. 

```rust
//rust
//this compiles and is valid rust
let x = 10;
let mut x = x; //x is not mutalbe
```

### :bangbang: `auto` vs. inferred

The `auto` keyword in C++ was introduced to enable backwards compatible type inferrence. Rust comes with type inferrence by default.

```c++
//c++
const auto x = 5;
auto y = 10;
```

```rust
//rust
let x = 5;
let mut y = 10;
```

### :bangbang: Casting

C++ includes many built-in implicit type casting (and conversions). In Rust, type casting must be explicit.

```c++
//c++
const double x = 1;  //implicit conversion from int to double
const auto y = static_cast<double>(1); //C++11
```

> There are many ways to cast in C++. I can't cover them all here.

```rust
//rust
let x: f64 = 5; //won't compile - no implicit conversion
let y = 5 as f64; //cast using as
```

### :x: Copy and Move Semantics

The philisophical differences between C++ and Rust involing copy and move semantics are very different. To summarize:

- C++ copies variables by default. If a class/struct has implemented move semantics, the value will be moved if possible
- Rust moves (transfers ownership) by default. Custom types can implement `Copy` (implicit) and `Clone` (explicit) copy semantics.

> See Also [References vs Borrowing](#references-vs-borrowing).

#### :interrobang: Copying and Moving Primitive Types

For primitive types like `int` and `bool`, both C++ and Rust make copies when assigning values.

```c++
//c++
const auto x = 5;
const auto y = x; //y holds a copy of 5
printf("x%d\n", x);
printf("y%d\n", y);
```

```rust
let x = 5;
let y = x; //y holds a copy of 5
//for these we assume `impl fmt::Display for X`
println!("x{}", x); 
println!("y{}", y);
```

#### :x: Copying and Moving Compound Types

For non-primitives, C++'s default mode is to copy variables where Rust's default is to move (or transfer ownership).  For example:

```c++
//c++
struct X {
  int value;
};
auto x = X{5};
auto y = std::move(x); //y holds a copy of x
printf("x%d\n", x); //C++ specifies a valid but unspecified state for x
printf("y%d\n", y);
```

```rust
struct X(i32);
let x = X(5);
let y = x; //y now owns '5' and x is now invalid (dropped)
//for these we assume `impl fmt::Display for X`
println!("x{}", x); // This is a compiler error because x is moved
println!("y{}", y);
```

In Rust, you can override the default behavior implemented `Clone` or `Copy` for a custom-type. The mechanics are similar to custom move/copy assign/construct operators in C++.

```c++
struct X {
  int value;
  X(const X&a){
    //magic here
  }
  X& operator=(const X&a){
    //magic here
    return *this;
  }
  X(const X&&a){
    //magic here
  }
  X& operator=(const X&&a){
    //magic here
    return *this;
  }
};
```

```rust
struct X(i32);
impl Copy for X {
  fn copy(self: &Self) -> Self {
    //magic here
  }
}
```

> `self: &Self` can be written:
> - `&self`: reading
> - `mut self`: mutating
> - `self`: consuming

Or you can use default `Copy`:

```rust
#[derive(Copy)]
struct X(i32);
let x = X(5);
let y = x; //y gets a copy of X
//for these we assume `impl fmt::Display for X`
println!("x{}", x); // This compiles now
println!("y{}", y);
```

#### :x: References vs Borrowing

C++ references and Rust borrows are very different but use the similar syntax.

```c++
//c++
auto x = 5;
auto & y = x; //y refers to x
```

In C++, `y` is basically a pointer to `x`. If `x` changes, `y` will reflect that change. If `x` goes out of scope, `y` will be a dangling reference (undefined behavior).


```rust
//rust
let mut x = 5;
let y = &x; //y borrows x's value
x = 10; //won't compile, because x is "borrowed"
println!("y{}", y);
x = 20; //will compile, borrow ends after last use
```

In Rust, `&` is a borrow. The assigned borrows the ownership and limits what can happen with the value while borrowed.

## :interrobang: Procedural Programming

### :interrobang: Flow Control

#### :interrobang: If/Else

```c++
//c++
auto is_true = true;
if( is_true ){
  //do this
} else {
  //do that
}
```

```rust
//rust
let is_true = true;
if is_true { //parentheses are optional but discouraged
  //do this
} else {
  //do that
}
```

`else if` basically works the same way in C++ and Rust.

> :interrobang:  The big different between `if`/`else` statements in Rust and C++ is that Rust `if`/`else` statements are expresssions that can be used directly to assign a value.

#### :bangbang: Ternary Operator

```c++
//c++
const auto x = true;
const auto y = x ? 5 : 10;
```

```rust
//rust
let x = true;
let y = if x {5} else {10};
```

In Rust, `if` is an expression and evaluates to the value. Notice there are no `;`'s in the branches. Both branches must resolve to the same type (just like C++ ternary).

#### :x: switch vs match

C++ `switch` and Rust `match` similar conceptually but have distinct implementations.

```c++
//c++
auto x = 5;
auto y = 0;
switch(x){
  case 0:
    y = 0;
    break;
  case 1:
    y = 1;
    break;
  default:
    y = 2;
    break;
}
```

```rust
//rust
let x = 5;
let mut y = 0;
match x {
  0 => y = 0;
  1 => y = 1;
  _ => y = 2; //_ is a catch-all value
};

#### :interrobang: Loops

##### :bangbang: For Loops

```c++
//c++
for(int i=0; i < 10; ++i){
  //do something (10x)
}

const auto list = {0,1,2,3,4};
for(const auto & value: list){
  //do something with each `value`
}
//list is still valid
```

```rust
//rust
for i in 0..10 { //use _ if i is not used
  //do something (10x)
}

let list = [0,1,2,3,4];
for value in &list { //borrow list
  //do something with each `value`
}
//borrow is returned
for value in list { //consume list
  //do something with each `value`
}
//at this point list has been consumed and cannot be used
```

##### :bangbang: While Loops

```c++
//c++
while(true){
  //forever
}

auto dont_stop = true;
while(dont_stop){
  dont_stop = false;
}
```

```rust
//rust
loop {
  //forever
}

let mut dont_stop = true;
while dont_stop == true {
  dont_stop = false;
}
```

> :interrobang: Rust, `loop` can be used as an expression where `break` returns a value. Rust also supports named `break`'s with nested loops.


### Functions

Functions in C++ and Rust are almost identical conceptually and vary only syntacically.

```c++
//c++
int function(int x){
  return x;
}
//with trailing return type (looks more like Rust)
auto function(int x) -> int {
  return x;
}

//callsite
function(5);
```

```rust
//rust
fn function(x: i32) -> i32 {
  return x;
}

//rust lines without a semicolon are an implicit return
fn function(x: i32) -> i32 {
  x // same as return x;
}

//callsite
function(5);
```

#### Default Arguments

```c++
//c++
auto do_something(int x = 2) -> int {
  return x*2;
}

//callsite
do_something(10); //or
do_something();
```

Rust does not support default arguments. 

> There are techiques using `Option` or `impl Default` that acheive similar behavior.

### :interrobang: Lamba's

C++ lambda's are called closures in Rust.

```c++
//c++
const auto x = 5;
//captured variables `&x` are explicit
auto lambda = [&x](int y){
  return x + y;
};

//callsite
const auto sum = lamdba(5);
```

```rust
//rust
let x = 5;
//captured values (x) automatically borrowed until closure's last use
let closure = |y: i32| -> i32 { x + y };

//callsite
let sum = closure(5);
```

#### :interrobang: Immediately called Lambda

In C++, I like to declare and immediately call a lambda to do complex logic in an isolated scope and return a `const` value from it.

```c++
//c++
auto list = [&](){
  auto result = std::vector<std::string>{};
  for(int i=0; i < 10; i++){
    result.push_back(std::string{"hello"});
  }
  return result;
}();
```

In Rust, a scope is an expression. So there is no need for a closure to do the same thing.

```rust
//rust
let list = {
  let mut result: Vec<String> = Vec::new();
  for _ in 0..10 {
    result.push(String::from("hello"));
  }
  result
};
```


## Object Orient Programming

## :x: Classes and Structs

> The primary difference between a C++ `class` and a `struct` is the `class` members are `private` by default and `struct` members are `public`. When comparing to Rust, we will just consider the C++ `struct`.

```c++
//c++
struct X {
  int width;
  int height;
}; //trailing ;

//or a tuple
using T = std::tuple<int, int>;
```

```rust
//rust
struct X {
  width: i32,
  height: i64, //this trailing comma is optional
} //no trailing ;

//struct can be a tuple
struct T{ i32, i64 }
```

### :interrobang: Member Functions/Methods

```c++
//c++
struct Foo {
  int value;
  void set_value(int num){
    value = num;
  }
};
```

```rust
//rust
struct Foo {
  value: i32
}

impl Foo {
  fn set_value(&self, num: i32){
    self.value = num;
  }
}
```

### Constructors

C++ gives a ton a flexibility in how to construct objects. Rust has exactly one way to construct an object. However, both languages support factory functions for combining logic with instantiation.

```c++
//c++
struct X {
  int width; //could assign defaults
  int height;
  X(int w, int h){ //could do an init-list here
    //do the magic
  }
  //or with a factory fucntion
  static X create_x(int w, int h){
    return X(w,h);
  }
};
```

```rust
//rust
struct X {
  width: i32,
  height: i64, //this trailing comma is optional
} //no trailing ;

impl X {
  fn create(w: i32, h: i64) -> Self {
    //do the magic

    //this is the ONLY way to officially construct X
    //provides the values for all members
    return X{width: w, height: h};
  }
}

//callsite
let x0 = X{width:10, height:20};
let x1 = X::create(5,10);
```

### :interrobang: Destructors

> :interrobang: C++ destructors have a lot more to worry about than Rust because of the way inheritance and copy/move semantics are implemented in C++.

```c++
//c++
struct X {
  int width; //could assign defaults
  int height;
  ~X(){
    //called when instance goes out of scope
    //take care to properly handle copy/move semantics and inheritance
  }

};
```

```rust
//rust
struct X {
  width: i32,
  height: i32
}

impl Drop for X {
  fn drop(&self){
    //called when instance is dropped
    //only called once per value
    //not called on borrow or ownership transfer
  }
}
```

### :interrobang: Access Modifiers

- Encapsulation
  - public, private, protected


## :x: Polymorphism

Generally, C++ uses polymorphism more liberally than Rust.

### :x: Function Overloading

#### :x: Function Signatures

```c++
//c++
struct Foo {
  int bar();
  int bar() const; //unique by const
  int bar(int value); //unique by parameter
  int rbar() &&; //unique by rvalue
  int rbar() &; //unique by lvalue
};
```

```rust
//rust
impl Foo {
  //functions need unique names
  fn bar();
  fn bar_const();
  fn bar_int(value: i32);
}
```

### :interrobang: Operator Overloading

### Function Overriding
- Virtual Funtions

- Interfaces and Traits
- Concepts and Bounds

## :x: Meta-Programming: Templates and Macros

- generics
  - functions
  - types
- concepts as Bounds
- Rust Macros

### 

## Standard Library

