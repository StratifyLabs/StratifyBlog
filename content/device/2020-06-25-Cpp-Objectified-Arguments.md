---
categories:
- Device Tips
date: "2020-06-25"
layout: post
tags:
- microcontroller
- cpp
- programming
title: C++ Objectified Arguments
---

Last year, I started experimenting with [strong types for C++ arguments]({{< relref "2019-08-16-Using-Strong-Type-Arguments-on-Embedded-Cpp.md" >}}). After a year of working trying various approaches, I think I have really found the sweet spot for creating APIs that are

- strongly typed,
- easy to use,
- and compile efficiently.

Here is a quick reminder of what a strongly typed API looks like.

```c++
// see the strong type argument post linked above for how to define Argument
using SourceFilePath = Argument<const char *, struct SourceFileTag>; 
using DestinationFilePath = Argument<const char *, struct DestinationFilePathTag>; 

int copy_file_weak(
    const char * source,
    const char * destination
    );

int copy_file_strong(
    SourceFilePath source,
    DestinationFilePath destination
    );

//then calling method looks like this
//order of the argument is obvious
copy_file_strong(
    SourceFilePath("/home/a.txt"),
    DestinationFilePath("/home/b.txt")
);

//hard to know which is the source/dest without checking the declartion
copy_file_weak(
    "/home/a.txt",
    "/home/b.txt"
);
```

The example above illustrates some of the pros and cons of using strongly typed arguments.

- Code is super easy to read  :thumbsup:
- Argument order is obvious when then looking at the implementation  :thumbsup:
- APIs are cumbersome to code  :thumbsdown:

An alternative approach is to define a lightweight class that packages up the arguments and provides setters and getters to access arguments. Consider the following example:

```c++
class CopyOptions {
public:

    CopyOptions& set_source(const char * value){
        m_source = value;
        return *this;
    }

    CopyOptions& set_destination(const char * value){
        m_destination = value;
        return *this;
    }

    const char * source() const {
        return m_source;
    }

    const char * destination() const {
        return m_destination;
    }

private:
    const char * m_source = nullptr;
    const char * m_destination = nullptr; //set to the default
};
static void copy_file_strong(const CopyOptions & options);
static void copy_file_weak(const char *  source, const char *  destination);

//strong call
//very obvious what the arguments are
//added bonus -- arguments an be in any order
//default argument values can be define in CopyOptions
copy_file_strong(
    CopyOptions()
    .set_source("/home/a.txt")
    .set_destination("/home/b.txt")
);

//same problems as before
copy_file_weak(
    "/home/a.txt",
    "/home/b.txt"
);
```

On the implementation side, this approach works much better. Code completion is especially nice because it picks up right away what options can be set. :thumbsup:

But we introduced a pretty cumbersome problem with the setters and getters :thumbsdown:. To solve that we create a getter/setter macro that looks something like this.

```c++
#define API_ACCESS_FUNDAMENTAL(c, t, v, iv) \
	public: \
	t v() const { return m_##v; } \
	c& set_##v(t value){ m_##v = value; return *this; } \
	private: \
	t m_##v = iv

class CopyOptions {
	API_ACCESS_FUNDAMENTAL(CopyOptions,const char *,source,nullptr);
	API_ACCESS_FUNDAMENTAL(CopyOptions,const char *,destination,nullptr);
};
```

Now that is much better.

So the next question is whether we are still efficient (:question:). To get an idea of the performance hit from this approach, I compiled a simple `HelloWorld` program (cross compiled to the ARM Cortex M architecture) and checked the binary output size.

```c++

#include <cstdio>
#include <sapi/api/ApiObject.hpp>

#define PASS_BY_OBJECT 1

#if PASS_BY_OBJECT
class ExecuteOptions {
	API_ACCESS_FUNDAMENTAL(ExecuteOptions,const char *,hello,nullptr);
	API_ACCESS_FUNDAMENTAL(ExecuteOptions,const char *,world,nullptr);
};
static void execute(const ExecuteOptions & options);
#else
static void execute(const char *  hello, const char *  world);
#endif

int main(int argc, char * argv[]){
#if PASS_BY_OBJECT
	execute(
				ExecuteOptions()
				.set_hello("Hello")
				.set_world("World")
				);
#else
	execute("Hello", "World");
#endif
	return 0;
}

#if PASS_BY_OBJECT
void execute(const ExecuteOptions & options){
	printf("%s %s\n", options.hello(), options.world());
}
#else
void execute(const char *  hello, const char *  world){
	printf("%s %s\n", hello, world);
}
#endif
```

To my surprise, the programs compiled to the exact same size (with optimaztion on, of course).

- `PASS_BY_OBJECT=1` code size: 548
- `PASS_BY_OBJECT=0` code size: 548

This example is particularly simple. If you are highly concerned about performance and code size, you will want to experiment. But nonetheless, this approach is a great way to write complex APIs with clear code that enforces strong argument types.

Last thing, using the `Argument` class for a strong `bool` argument is still worthwhile.  Consider this code:

```c++
remove(true); //hmmm, what is true? what does that mean?
remove(IsRecursive(true)); //much better, recursive remove

//header looks like this
using IsRecursive = Argument<bool, struct IsRecursiveTag>; 
int remove(IsRecursive is_recursive);
```

There are some other places where `Argument` works well, but if it isn't used sparingly, the code becomes quite cumbersome.
