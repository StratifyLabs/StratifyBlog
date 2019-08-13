---
categories:
- Embedded C Tutorial
date: "2013-12-11"
layout: post
number: 10
page_source: EmbeddedC
tags:
- C tutorial
title: Order of Operations Reference
---

The following is a list of the C order of operations from highest precedence to lowest.  
Operators within the same group have equal precedence and evaluate left-to-right
or right-to-left as indicated.

### Left-to-Right

- `++` post increment
- `--` post decrement
- `()` function call
- `[]` array subscripting
- `.` structure/union member selection
- `->` element selection via pointer

#### Examples

```cpp
int x[0];
x[0] = 1;
x[1] = 2;
int * i = x;
int y;
y = *i++ //y is 1 and i points to x[1]
(*i)++ //now x[1] is 3 and i points to x[1]
```

```cpp
char x[4]; //4 bytes, one 32-bit word
(uint32_t*)x[0]; //returns a char value 0 to 0xff cast as a pointer
((uint32_t*)x)[0]; //returns an 32-bit word
```

### Right-to-Left

- `++` pre increment
- `--` pre decrement
- `+` unary plus
- `-` unary minus
- `!` logical not
- `~` bitwise not
- `(<type>)` type casting
- `*` dereference
- `&` address of
- `sizeof()` sizeof type or variable

#### Examples

```cpp
struct abc {
    int a;
    int b;
    short c;
};
struct abc x;
struct abc * y = &x;
&x.b
//is the same as
&(x.b)

&y->b;
//is the same as because -> is stronger than &
&(y->b);

(int*)&x[0];
//this is an error -- could use
((int*)&x)[0];
```

### Left-to-Right

- `*` multiplication
- `/` division
- `%` modulo

#### Examples

```cpp
a + b * x + y
//is the same as
a + (b * x) + y
```

### Left-to-Right

- `+` addition
- `-` subtraction

#### Examples

```cpp
c >> a * b + x * y
//is the same as
c >> ((a * b) + (x * y))

a + b * x + y
//is the same as
a + (b * x) + y
```

### Left-to-Right

- `>>` bitwise shift right
- `<<` bitwise shift left

#### Examples

```cpp
a + b >> x + y
//is the same as
(a + b) >> (x + y)
```

### Left-to-Right

- `<` less than
- `<=` less than or equal to
- `>` greater than
- `>=` greater than or equal to

```cpp
if( a < b == x > y ){}
//is equivalent to
if( (a < b) == (x > y)){}
```

### Left-to-Right

- `!=` not equal to
- `==` equal to

### Left-to-Right

- `&` bitwise and

```cpp
x & 0x2 == 0
//is equivalent to
x & (0x2 == 0) 
//fix it using ()
(x & 0x2) == 0

a + b & 0x02 == x & 0x02 + y
//is the same as -- when in doubt use ()
(a + b) & (0x02 == x) & (0x02 + y)
```

### Left-to-Right

- `^` bitwise xor

### Left-to-Right

- `|` bitwise or

#### Examples

```cpp
a | b && x | y
//is the same as 
(a | b) && (x | y)
```

### Left-to-Right

- `&&` logical and

#### Examples

```cpp
a == b && x == y
//is the same as 
(a == b) && (x == y)

a && b == x && y
//is the same as 
a && (b == x) && y

a && b || x && y
//is the same as 
(a && b) || (x && y)

a && b | x && y
//is the same as 
a && (b | x) && y
```

### Left-to-Right

- `||` logical or

#### Examples

```cpp
a || b && x || y
//is the same as 
a || (b && x) || y
```

### Left-to-Right

- `?`: ternary operator

```cpp
a = x > y ? x : y;
//is the same as
if( x > y ){
    a = x;
} else {
    a = y;
}

a = x > y ? x >>= z : y;
//is the same as
x >>= z;
if( x > y ){
    a = x;
} else {
    a = y;
}
```

### Right-to-Left

- `=` assign
- `+=` assign sum
- `-=` assign difference
- `\*=` assign product
- `/=` assign quotient
- `%=` assign remainder
- `<<=` assign shift left
- `>>=` assign shift right
- `&=` assign and
- `^=` assign xor
- `|=` assign or

#### Examples

```cpp
a = b == c
//is the same as
a = (b == c)

a = b += c
//is the same as (right to left evaluation)
a = (b += c)
//or
b += c;
a = b;
```


### Left-to-Right

- `,` comma (evaluates to the value of the second argument)

#### Examples

```cpp
//avoid using comma's as an operator :)
```