---
categories:
- Device Tips
date: "2021-04-29"
layout: post
tags:
- microcontroller
- cpp
- programming
title: Ten C++ Advantages over C in Embedded Systems
---

I have been using C++ to program microcontrollers for over 10 years now. I have found some really big advantages of C++ over C. So much so, that I claim the jump from C to C++ is almost as substantial as from assembly to C.

Here are 10 advantages of C++ over C for everyday microcontroller programming.

1. Object-Oriented Programming (OOP)
1. Stronger Types
1. Template Programming
1. RAII
1. Stronger Types
1. Polymorphism
1. constexpr
1. References
1. namespaces
1. Symbol Mangling

## 1. Object Oriented Programming

Object-oriented programming can be done in C. But it is tedious. If we look at a POSIX mutex we can see this. This pattern of passing an object pointer is extremely common (and tedious) in C.

```c++
//C
int pthread_mutex_lock(pthread_mutex_t *mutex);
int pthread_mutex_unlock(pthread_mutex_t *mutex);

//usage
pthread_mutex_t mutex0;
pthread_mutex_init(&mutex0, NULL);
//passing the argument leaves room for error and bugs
pthread_mutex_lock(&mutex0); 
// do some mutually exclusive stuff
pthread_mutex_unlock(&mutex0); 
pthread_mutex_destory(&mutex0);
```

In C++, the compiler will manage the data object for you. Consider this Mutex class.

```c++
class Mutex {
  Mutex(){
    pthread_mutex_init(&m_mutex);
  }

  ~Mutex(){
    pthread_mutex_destroy(&m_mutex);
  }


  int lock(){
    return pthread_mutex_lock(&m_mutex);
  }
  int unlock(){
    return pthread_mutex_unlock(&m_mutex);
  }
private:
  //the mutex is tied to the methods that use it
  //this means it can't be passed to the wrong function
  pthread_mutex_t m_mutex;
};

//usage
Mutex mutex0; //automatically initialized
mutex0.lock(); //impossible to pass the wrong value
mutex0.unlock(); //unlock before destroying
//automatically destroyed when Mutex goes out of scope

```

C++ is more concise and less prone to mistakes. If you multiply this simple approach by many thousands of lines of code. You get a HUGE improvement.

## 2. Stronger Types

When it comes to types, C is much more permissive than C++. This is great if you are lazy. But horrible if you want to eliminate bugs in your code. Where C will give you a warning (or just be silent), C++ will produce and error and force you to inspect the issue. This moves debugging from runtime (in C) to compile time in C++. The code below will compile without errors in C, but will fail compilation in C++.

```c++
int square(int * num){
  return *num * *num;
}

int main(){
  void * x = 0;
  square(x);
}
```

This is a subtle way to make the compiler do more of the work to ensure your program is correct.

## 3. Template Programming

In C++, you can use templates to have variable types for a function. Consider this digital filter in C:

```c++
//C
typedef struct {
  int average;
  int weight;
} filter_int_t;
int filter_int(filter_int_t * filter, int input){
  //int based implementation
}

typedef struct {
  float average;
  float weight;
} filter_float_t;
int filter_float(filter_float_t * filter, int input){
  //float based implementation
}

//and so on
```

C++ is more concise, the exact some code can be used for a variety of types. The compiler then handles generating the function for different types.

```c++
template<typename NumberType> class Filter {
public:
  NumberType filter(NumberType input){
    //implement here
  }

private:
  NumberType m_average;
  NumberType m_weight;
};

Filter<float> f_filter;
Filter<int> i_filter;
Filter<long int> li_filter;
Filter<double> d_filter;
```

## 4. RAII

This is one of my favorites parts of using C++ over C. The terribly named "Resource Allocation is Initialization" means that every class has a constructor that allocates resources and a destructor that cleans them up. Both are called automatically by the compiler. The constructor is called when the `class` variable is declared, and the destructor is called when it goes out of scope.

Here is an example in C (using the mutex again).

```c++
int make_magic_happen(int value){
  pthread_mutex_lock(&mutex0); 

  if( value < 0 ){
    //for magic, we return if value < 0
    //don't forget the mutex
    pthread_mutex_unlock(&mutex0); 
    return -1;
  }

  if( value == 10 || value == 100 ){

    pthread_mutex_unlock(&mutex0); 
    return 1;
  }
  //now really make some magic

  //don't forget the mutex!
  pthread_mutex_unlock(&mutex0); 
  return 0;
}
```

In C, you always need to unlock the mutex manually. So if you have multiple exit points (in this simple case), it can be a pain. This C++ class enables auto lock/unlock using RAII.

```c++
class MutexScope {
public:
  MutexScope(Mutex & mutex) : m_mutex(mutex){
    m_mutex.lock();
  }
  ~MutexScope(){
    m_mutex.unlock();
  }
private:
  Mutex m_mutex;
}

int make_magic_happen(int value){
  //mutex is locked here and unlock automatically when the function returns
  MutexScope ms(mutex); 

  if( value < 0 ){
    //for magic, we return if value < 0
    return -1;
  }

  if( value == 10 || value == 100 ){
    return 1;
  }

  //now really make some magic
  return 0;
}
```

I have a whole list of examples where I have used [RAII in embedded applications]({{< relref "2021-03-25-Actual-Examples-of-RAII-in-embedded-cpp.md" >}}).

## 5. `enum class`

The `enum class` in C++ is a strong type that the compiler enforces. It is stronger than an `enum` in C++ which is stronger than an `enum` in C.

```c++
//C
enum value {
  //these have global scope so they need the prefix
  value_one,
  value_two,
  value_three
};

//in C this will compile (and is terrible)
int x = value_one;
enum value v = x + 5;

//C++
enum class Value {
  //scope to Value so no prefix
  one, two, three
};


//in C++ these won't compile
int x = Value::one;
Value value = x + 5;
```

If you have a variable with a small number of possible values, the `enum class` is a compiler enforced way to make sure variables of that type are only assigned allowed values. The `enum class` also prohibits operators (unless you create some).


## 6. Polymorphism

Unlike RAII, polymorphism is an awesome name. It literally means multiple forms and is used frequently in genetics to refer to genes that can manifest in different forms. In C++, it means code can take multiple forms based on the context. For example, `==` can mean different things in C++ depending on what is being compared. Strings are a really good example of polymorphism.

```c++
int x = 0;
int y = 0;
const char * hello = "hello";
const char * world = "world";

//in C == always means a numerical comparison
if( x == y ){}

//To compare strings we convert the compare to a number
if( strcmp(hello, world) == 0 ){
  //to make is worse, you shoudl use strncmp()
}

//C++ comparing == is polymorphic (is takes a different form in diffent places)
if( x == y ){
  // == means compare numbers
}

if( std::string(hello) == std::string(world) ){
  // == means compare strings
}
```

Changing what operators (like `==`, `+`, `-`, etc) do is also referred to as overloading an operator. This is just one type of polymorphism in C++. When you re-implement a virtual method, that is another way C++ uses polymorphism. Polymorphism goes a long way to make code more concise and readable. It also means less room for mistakes (like not using `strncmp()`).

## 7. `constexpr`

In C, the use of pre-processor macros is abundant. And for C, it is generally good practice. However, it can be a big pain to debug and causes scoping problems.

C++ has a `constexpr` keyword that allows you to tell the compiler that the value must be evaluated at compile time.

```c++

//C
#define X_VALUE 10

//C++
static constexpr int x_value = 10;
```

The advantages:

1. `x_value` has a type, so the compiler can enforce type compatibility. `X_VALUE` has no type.
2. `x_value` can be scoped as narrowly as needed (inside a function, class namespace or global). `X_VALUE` cannot be scoped.
3. `x_value` can be used in a more complex `constexpr` evalution where the compiler can give specific errors. Using `X_VALUE` in a complex macro can result in difficult-to-debug compiler errors.

## 8. References

In C, you can pass by value or pass a pointer (by value). C++ has references which are basically more strict pointers. The big difference is that pointers can be `null` and references cannot. So the compiler can do a much better job stopping the build if you have not referred to a value correctly. In C, you need to catch `null` or other bad pointers during runtime.

```c++
typedef struct {
  int x[3];
  int y[3];
  int z[3];
} my_data_t;  

my_data_t my_data;

//C pointer passing
void process(my_data_t * data);
process(&my_data); //passed as a pointer

//c++ refererence passing
void process(my_data_t & data);
process(my_data); //passed as if it was a pointer
```

## 9. Symbol Scoping

In C, you end up doing a lot of manually prefixing. I usually do something like this for example, `logic.h`.

```c++
//C
int logic_initialize(void * logic_object);
int logic_finalize(void * logic_object);
int logic_read(void * logic_object, void * buffer, int nbyte);
int logic_write(void * logic_object, const void * buffer, int nbyte);

//C++
class Logic {
public:
  Logic(){
    //initialize here
  }
  ~Logic(){
    //finalize here
  }

  //these names are scoped to the Logic class
  //so they can be re-used without prefixing in other classes
  int read(void * buffer, int byte);
  int write(const void * buffer, int nbyte);
private:
  void * m_object;
}
```

Namespaces are a way of creating a scope without creating a class. These are great for large projects or frameworks to ensure there are no naming collisions.

## 10. Symbol Mangling

In C, object level symbols are generated by function name, but C++ accounts for both the name of the function and its arguments. It creats a `mangled` symbol that is unique. This is fantastic for designing APIs. Consider these examples:

```c++
//C
int write(const void * buffer, int nbyte);
int write_location(int loc, const void * buffer, int nbyte);

//C++
int write(const void * buffer, int nbyte);
int write(int loc, const void * buffer, int nbyte);
```