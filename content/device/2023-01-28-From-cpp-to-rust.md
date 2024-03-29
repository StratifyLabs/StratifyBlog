---
categories:
- Device Tips
date: "2023-01-28"
layout: post
tags:
- cpp
- programming
- microcontroller
- cortex-m
- linux
- rust
title: From C++ to Rust
---

I recently started learning Rust. As a long-time C++ programmer, I wanted to understand how C++ concepts worked in Rust. This is a comparison of various concepts and implementation details of C++ and Rust.

I rate each aspect of the language as:

| Indicator | Concept | Implementation |
|--|--|--|
| :white_check_mark: | Highly Similar | Minor Differences|
| :question: | Somewhat Similar | Substantial Differences|
| :x: | Vaguely Similar | Major Differences|

> This is not an exhaustive comparison.
- [Variables](#variables)
  - [:x: Naming Conventions](#-naming-conventions)
  - [:white_check_mark: Primitive Types](#-primitive-types)
    - [:white_check_mark: Literals](#-literals)
    - [:x: User-Defined Literals](#-user-defined-literals)
  - [:question: Compound Data Types](#-compound-data-types)
    - [:x: Enums](#-enums)
    - [:question: Arrays](#-arrays)
    - [:question: Tuples and Structs](#-tuples-and-structs)
  - [:white_check_mark: Constness and Mutability](#-constness-and-mutability)
  - [:white_check_mark: `auto` vs. inference](#-auto-vs-inference)
  - [:white_check_mark: Casting](#-casting)
  - [:x: Copy and Move Semantics](#-copy-and-move-semantics)
    - [:x: Copying and Moving Compound Types](#-copying-and-moving-compound-types)
    - [:x: References vs Borrowing](#-references-vs-borrowing)
- [Procedural Programming](#procedural-programming)
  - [:question: Flow Control](#-flow-control)
    - [:question: If/Else](#-ifelse)
    - [:white_check_mark: Ternary Operator](#-ternary-operator)
    - [:x: switch vs match](#-switch-vs-match)
    - [:question: Loops](#-loops)
      - [:question: For Loops](#-for-loops)
      - [:question: While Loops](#-while-loops)
      - [:x: Loops](#-loops)
  - [:white_check_mark: Functions](#-functions)
    - [:x: Default Arguments](#-default-arguments)
  - [:question: Lambas](#-lambas)
    - [:question: Immediately called Lambda](#-immediately-called-lambda)
- [Object Orient Programming](#object-orient-programming)
  - [:x: Classes and Structs](#-classes-and-structs)
    - [:question: Member Functions/Methods](#-member-functionsmethods)
    - [:x: Constructors](#-constructors)
    - [:question: Destructors](#-destructors)
    - [:question: Access Modifiers](#-access-modifiers)
    - [:x: Inheritance](#-inheritance)
    - [:question: Virtual Functions](#-virtual-functions)
- [Overriding and Overloading](#overriding-and-overloading)
  - [:x: Function Overloading](#-function-overloading)
  - [:white_check_mark: Operator Overloading](#-operator-overloading)
  - [:question: Function Overriding](#-function-overriding)
- [Meta-Programming: Generics, Templates, and Macros](#meta-programming-generics-templates-and-macros)
  - [:question: Generics](#-generics)
  - [:question: C++20 Concepts vs Rust Bounds](#-c20-concepts-vs-rust-bounds)
  - [:x: Meta-Programming and Macros](#-meta-programming-and-macros)
- [The End](#the-end)

## Variables

### :x: Naming Conventions

Rust specifies `snake_case` for variable and function names. It specifies `UpperCamelCase` for custom types and `UPPER_CASE` for constant values. The Rust compiler issues warnings if the case conventions are not followed. While most projects follow similar conventions, C++ does not specify case conventions.

### :white_check_mark: Primitive Types

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

#### :white_check_mark: Literals

| C++ | Rust | Type | 
|-----|------|------|
| `5'000` | `5_000` | Decimal |
| `0xff` | `0xff` | Hexadecimal |
| `O777` | `0o777` | Octal |
| `0b1111'0000` | `0b1111_0000` | Binary |
| `'A'` | `'A'` | Byte |
| `1.0f` | `1.0_f32` | single-precision floating point |
| `1.0` | `1.0` | double-precision floating point |

With Rust you can suffix a literal with an underscore-primitive type:

```rust
//rust
let x = 5_u64;
```

#### :x: User-Defined Literals

```c++
//c++
auto operator"" _milliseconds(unsigned long long int value) -> long long int {
  return value;
}

const auto value = 5_milliseconds;
```

In Rust, you can have user-defined literals only within macros. Doing so is non-trivial but the call site could look something like this:

```rust
//rust
//can do this
let duration = chrono!(5milliseconds);
//can't do this
let other_duration = 5milliseconds;
```

### :question: Compound Data Types

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

An `enum` in Rust can also be used similarly to `std::variant`.

```c++
using Option = std::variant<bool, int>;

const auto option = Option{5};
const auto nothing = Option{false};
```

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

> Rust `std::Option` is used like C++'s `std::optional`. Some aspects of Rust `std::Option` are built into the language to streamline error handling using the `?` operator.

#### :question: Arrays

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
let ub = x[5]; //won't compile 
//if a dynamic out-of-range value is used, rust will panic at runtime
```

#### :question: Tuples and Structs

In Rust, tuples are a native type. In C++, they are part of `std::tuple`.

```c++
//c++
auto get_tuple() -> std::tuple<int, int>{
  return std::tuple<int, int>{1, -1};
}
```

```rust
//rust
fn get_tuple() -> (i32, i32) {
    (1, -1)
}
```

### :white_check_mark: Constness and Mutability

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

In Rust, variables are dropped after the last use. This means a variable can be re-declared in the same scope. 

```rust
//rust
//this compiles and is valid rust
let x = 10;
let mut x = x; //x is now mutable
```

### :white_check_mark: `auto` vs. inference

The `auto` keyword in C++ was introduced to enable backward compatible type inference. Rust comes with built-in type inference.

```c++
//c++
const int w = 5;
const auto x = 5;
int y = 10;
auto z = 10;
```

```rust
//rust
let w: i32 = 5;
let x = 5;
let mut y: i32 = 10;
let mut z = 10;
```

### :white_check_mark: Casting

C++ includes many implicit type-casts. In Rust, type-casting is always explicit.

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

Copy and move semantics between C++ and Rust concerning are very different. To summarize:

- C++ copies variables by default. If a `class`/`struct` has implemented move semantics, the value is sometimes moved.
- Rust moves (transfers ownership) by default. Custom types can implement `Copy` (implicit) and `Clone` (explicit) copy semantics.

Rust primitive types implement `Copy` and `Clone` such that primitives are copied by default (just like in C++).

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

> See Also [References vs Borrowing](#references-vs-borrowing).

#### :x: Copying and Moving Compound Types

For non-primitives, C++'s default mode is to copy variables whereas Rust's default is to move (or transfer ownership).  For example:

```c++
//c++
struct X {
  int value;
};
auto x = X{5};
auto y = std::move(x); //y holds a copy of x (default move is to copy)
printf("x%d\n", x); //C++ specifies a valid but unspecified state for x
printf("y%d\n", y);
```

```rust
struct X(i32);
let x = X(5);
let y = x; //y now owns '5' and x is now invalid
//for these we assume `impl fmt::Display for X`
println!("x{}", x); // This is a compiler error because x is moved
println!("y{}", y);
```

In Rust, you can override the default behavior by implementing `Clone` or `Copy` for a custom type. The mechanics are similar to custom move/copy assign/construct operators in C++.

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

Or you can use the default `Copy`:

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

C++ references and Rust borrows are very different but use similar syntax.

```c++
//c++
auto x = 5;
auto & y = x; //y refers to x
```

In C++, `y` is a pointer to `x`. If `x` changes, `y` will reflect that change. If `x` goes out of scope, `y` will be a dangling reference (undefined behavior).


```rust
//rust
let mut x = 5;
let y = &x; //y borrows x's value
x = 10; //won't compile, because x is "borrowed"
println!("y{}", y);
x = 20; //will compile, borrow ends after last use
```

In Rust, `&` is a borrow. The borrower takes ownership of the underlying data and limits what the original variable can do.

> The above is a simplification of Rust ownership. [Read more in the Rust book](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html)

## Procedural Programming

Most procedural programming concepts in Rust are similar to C++. A few concepts differ substantially such as Rust's `match` keyword.

### :question: Flow Control

#### :question: If/Else

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

`else if` works the same way in C++ and Rust.

#### :white_check_mark: Ternary Operator

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

In Rust, `if` is an expression that evaluates to a value. Notice there are no `;`'s in the branches. Both branches must resolve to the same type (just like C++ ternary).

#### :x: switch vs match

C++ `switch` and Rust `match` are similar conceptually but have distinct implementations.

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
```

In Rust, the `match` statement can pattern match enums in a way that is unparalleled in C++.

```rust
let x = Some(5); //is a std::Option
match x {
  Some(value) => println!("Has value {}", value),
  None => println!("doesn't have a value")
};
```

#### :question: Loops

##### :question: For Loops

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

##### :question: While Loops

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
//this is a warning in rust - use a `loop`
while true {
  //forever
}

let mut dont_stop = true;
while dont_stop == true {
  dont_stop = false;
}
```

##### :x: Loops

Rust has a `loop` keyword that loops forever without an explicit `break`s. Rust also supports named `break`'s with nested loops.

```rust
//rust
loop {
  //forever loop
}
```

### :white_check_mark: Functions

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

#### :x: Default Arguments

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

> There are techniques using `Option` or `impl Default` that achieve similar behavior.

### :question: Lambas

C++ lambdas are called closures in Rust.

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
//captured values, `x` in this case, are automatically 
//borrowed until closure's last use
let closure = |y: i32| -> i32 { x + y };

//callsite
let sum = closure(5);
```

#### :question: Immediately called Lambda

In C++, I like to declare and immediately call a lambda to do complex logic in an isolated scope and return a `const` value from it.

```c++
//c++
const auto list = [&](){
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

### :x: Classes and Structs

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
struct T(i32, i64)
```

#### :question: Member Functions/Methods

```c++
//c++
struct Foo {
  int value;
  void set_value(int num){
    value = num;
  }
};

auto foo = Foo{10};
foo.set_value(5);
```

```rust
//rust
struct Foo {
  value: i32
}

//implementation is always separate from the data
//with no forward declarations required
impl Foo {
  fn set_value(&mut self, num: i32){
    self.value = num;
  }
}

let mut foo = Foo{value: 10};
foo.set_value(5);
```

#### :x: Constructors

C++ gives a ton of flexibility in how to construct objects. Rust has exactly one way to construct an object. However, both languages support factory functions for combining logic with instantiation.

```c++
//c++
struct X {
  int width; //could assign defaults
  int height;
  X(int w, int h){ //could do an init-list here
    //do some magic
  }
  //or with a factory function
  static X create_x(int w, int h){
    //do some magic
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
    //do some magic
    //this is the ONLY way to officially construct X
    return X{width: w, height: h};
  }
}

//callsite
let x0 = X{width:10, height:20};
let x1 = X::create(5,10);
```

#### :question: Destructors

> :question: C++ destructors have a lot more to worry about than Rust because of the way inheritance and copy/move semantics are implemented in C++.

```c++
//c++
struct X {
  int width; //could assign defaults
  int height;
  ~X(){
    //called when the instance goes out of scope
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
    //called when the instance is dropped
    //only called once per value
    //not called on borrow or ownership transfer
  }
}
```

#### :question: Access Modifiers

```c++
//c++
struct Foo {
public: //this is the default for struct
  int public_value;
protected:
  int protected_value;
private:
  int private_value;
}
```

```rust
//rust
struct Foo {
  pub public_value: i32,
  //no equivalent for protected
  private_value: i32
}
```

> In Rust, everything in the same module of the same crate can access private values. In C++, only the members of the same type can access private values.

#### :x: Inheritance

```c++
struct Foo {
  int foo;
  int get_foo() const {
    return foo;
  }
};

struct Bar : public Foo {
  int bar;
  int get_bar() const {
    return bar;
  }
};

auto bar = Bar{};
bar.get_foo();
bar.get_bar();
```

Rust uses "Traits" to define interfaces. A `struct` can implement multiple traits. "Traits" allow
- constants
- functions
- types

They do not allow data members. To do approximately the above in Rust:

```rust
//rust
trait Foo {
  fn get_foo() -> i32;
}

trait Bar {
  fn get_bar() -> i32;
}

struct FooBar {
  foo: i32,
  bar: i32
}

impl Foo for FooBar {
  fn get_foo(&self) -> i32 {
    return self.foo;
  }
}

impl Bar for FooBar {
  fn get_bar(&self) -> i32 {
    return self.bar;
  }
}

let foobar = FooBar{foo: 5, bar: 10};
let _foo = foobar.get_foo();
let _bar = foobar.get_bar();
```

#### :question: Virtual Functions

```c++
//c++
struct Foo {
  virtual int pure_vitual() = 0;
  virtual int virtual_with_default(){
    return 0;
  }
}

struct Bar: public Foo {
  int pure_virtual() override (){
    return 1;
  }
  int virtual_with_default() override (){
    return 1;
  }
}
```


```rust
//rust
trait Foo {
  fn pure_virtual() -> i32;
  fn virtual_with_default() -> i32 {
    return 0;
  }
}

struct Bar;

impl Foo for Bar {
  fn pure_virtual() -> i32 {
    return 1;
  }
  //omit to use Foo's default implementation
  fn virtual_with_default() -> i32 {
    return 1;
  }
}
```

## Overriding and Overloading

C++ uses polymorphism more liberally than Rust.

### :x: Function Overloading

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
  //functions can't be overloaded (need unique names)
  fn bar(){}
  fn bar_const(){}
  fn bar_int(value: i32){}
}
```

### :white_check_mark: Operator Overloading

```c++
//c++
struct X {
  int width; //could assign defaults
  int height;
  X operator+(const X & rhs) const {
    return X{width+rhs.width, height+rhs.height};
  }

};

//callsite
const auto x = X{5,5} + X{10,10};
```

```rust
//rust
struct X {
  width: i32,
  height: i32
}

impl std::ops::Add for X {
  fn add(&self, rhs: &X) -> Self {
    return X{ width: self.width + rhs.width,
      height: self.height + rhs.height};
  }
}

//callsite
let x = X{width:5, height:5} + X{width:10, height:10};
```

### :question: Function Overriding

```c++
//c++
struct Foo {
  void do_something(){
    printf("foo something\n");
  }
}

struct Bar: public Foo {
  //overrides Foo's default implementation
  void do_something(){
    printf("bar something\n");
  }
}
```

```rust
//rust
trait Foo {
  fn do_something(&self){
    println!("foo something");
  }
}
struct Bar;

impl Foo for Bar {
  //overrides Foo's default implementation
  fn do_something(&self){
      println!("bar something");
  }
}
```

## Meta-Programming: Generics, Templates, and Macros

### :question: Generics

```c++
//c++
//generic struct
template<typename Type> struct Foo {
  Type member_of_type;
};

struct Bar {
  //generic function
  template<typename Type> auto do_something(Type a) -> Type {
    return a;
  }
};
```

```rust
//rust
//generic struct
struct<Type> Foo {
  member_of_type: Type
}

//generic function
fn do_something<Type>(Type a) -> Type {
  return a;
}
```

### :question: C++20 Concepts vs Rust Bounds


```c++
//c++
template <typename Type> 
requires std::integral<Type> //ensure T matches %d formatting
auto printer(Type t) -> void {
  printf("%d", t);
}
```

```rust
//rust

//T must implement the Display trait
fn printer<Type: Display>(t: Type) {
    println!("{}", t);
}
```

### :x: Meta-Programming and Macros

It is hard to draw parallels between meta-programming (code that generates code) in C++ and Rust. Rust macros are used for meta-programming. They are used more like C++ template `consteval`/`constexpr` meta-programming than C++ preprocessor macros. While C++ pre-processor macros are discouraged, Rust macros are an integral part of the language.

Rust macros use rust code (compiled and executed at compile-time) that parses macro tokens and generate rust code which is then compiled and executed at run-time.

```rust
//rust

fn main(){
  println!("{} {}", "hello", "world");
}
```

In rust, the `println!()` macro has rust code that parses everything inside the `()` and then generates the rust implementation to achieve the desired operation. The code that parses and generates is also fully-fledged rust code that is compiled and executed at compile time.

## The End

After spending a couple of months investigating Rust, I found many things about the language to be delightful compared to C++.

- Better defaults: const vs mutable, implicit vs explicit conversions, copy vs move
- More cohesive ecosystem: Rust has a built-in build system, formatter, language server, documentation generator, unit testing, and package manager
- Simpler, more expressive syntax.

Rust has benefitted from several decades of lessons learned from C++. Herb Sutter recently introduced [cppfront](https://github.com/hsutter/cppfront) as a way to make C++ 10 times simpler and safer. Rust might have beaten him to it.

You can learn more about Rust here:

- [Rust Book](https://doc.rust-lang.org/stable/book/)
- [Rust by Example](https://doc.rust-lang.org/stable/rust-by-example/)
- [Comprehensive Rust in 4 Days](https://google.github.io/comprehensive-rust/welcome.html) (by Google)