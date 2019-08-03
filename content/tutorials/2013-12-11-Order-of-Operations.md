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

### Left-to-Right

- `*` multiplication
- `/` division
- `%` modulo

### Left-to-Right

- `+` addition
- `-` subtraction

### Left-to-Right

- `>>` bitwise shift right
- `<<` bitwise shift left

### Left-to-Right

- `<` less than
- `<=` less than or equal to
- `>` greater than
- `>=` greater than or equal to

### Left-to-Right

- `!=` not equal to
- `==` equal to

### Left-to-Right

- `&` bitwise and

### Left-to-Right

- `^` bitwise xor

### Left-to-Right

- `|` bitwise or

### Left-to-Right

- `&&` logical and

### Left-to-Right

- `||` logical or

### Left-to-Right

- `?`: ternary operator

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

### Left-to-Right

- `,` comma (evaluates to the value of the second argument)
