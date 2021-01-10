---
categories:
- Device Tips
date: "2020-12-14"
layout: post
tags:
- microcontroller
- cpp
- programming
title: Thread Local Error Contexts in Embedded C++ 
---

I recently rewrote a family of C++ libraries that I had been using for 10 years. The main themes of the rewrite include:

- **Thread Local Error Contexts**
- [Method Chaining]({{< relref "2020-12-15-Method-Chaining-in-Cpp.md" >}})
- [Strong Arguments]({{< relref "2019-08-16-Using-Strong-Type-Arguments-on-Embedded-Cpp.md">}})
- [RAII Everywhere]({{< relref "2020-12-22-RAII-Everywhere-in-Cpp.md" >}})
- [Filesystem Inspired Abstraction]({{< relref "2020-12-29-Filesystem-Inspired-Abstraction-in-embedded-cpp.md" >}})

# New Approach to Error Handling

The first them tackled in the rewrite was error-handling. Of course, this comes with the no-exceptions caveat because this code needs to run on highly constrained systems. Previously, I was taking the classic C approach to error handling or returning a negative value if there was an error and zero or greater for non-error. What I hated the most about the previous approach was the needed to cascade the error up and down the call graph until it could be handled.

I also considered adding an error messsage to a base class. But this would mean every class would have its own set of errors, and every object would need to be error checked.

After being unsatisfied with these classical approaches, I tried to break down exactly what I was trying to capture with the errors. As a result I came up with some important insights:

- Each execution thread should only allow one error at a time.
- All programming errors should be fixed immediately
- User errors (like telling the program to open a folder that doesn't exist) are handled in an application-specific way.

# Use One Error per Execution Thread

Rather than cascading errors by examining return values or checking objects for errors, the error context can be stored as a thread local value. For single-threaded applications, that is just one error context per program.

## The Error Context

Since there is just one error context per thread, we can embellish the context a bit without paying a big penalty (like we would for one error context per object).

The error context should contain all the information needed to quickly find and fix the error (programming errors). Or to tell the user what they did wrong (user errors).

```c++
class ErrorContext {
  // set this number based on system constraints
  static constexpr size_t m_backtrace_buffer_size = 32;

  void *m_signature; //used to identify the per-thread context
  int m_error_number = 0; //errno value when error occured
  int m_line_number = 0; //line number error occurred on
  char m_message[m_message_size + 1]; //message set on error 

  //back trace buffer to pinout the error
  void *m_backtrace_buffer[m_backtrace_buffer_size]; 
  size_t m_backtrace_count = 0;
};
```

Achieving thread local storage on POSIX systems is actually quite simple. Because `errno` is thread-safe, you can look at `&errno` to determine what error context you are in. The `m_signature` value is assigned to `&errno`. Even if you aren't using POSIX, newlib uses thread-safe `errno` so the approach works on any newlib run-time as well.

```c++
static ErrorContext m_error; //used for first thread
std::vector<ErrorContext> m_error_list; //used for other threads

ErrorContext &get_error() {
  if (&(errno) == m_error.m_signature) {
    return m_error;
  }

  if (m_error_list == nullptr) {
    m_error_list = new std::vector<Error>();
    API_ASSERT(m_error_list != nullptr);
  }

  for (Error &error : *m_error_list) {
    if (error.m_signature == &(errno)) {
      return error;
    }

    if (error.m_signature == nullptr) {
      error.m_signature = &(errno);
      return error;
    }
  }

  m_error_list->push_back(Error(&(errno)));
  return m_error_list->back();
}
```


## The #1 Rule

When using one error context per thread, you can only change the error context if it is non-erroneous. So any method that can affect the error context must be aborted if the thread has an error.

```c++
File& File::open(const char * path){
    //abort if the context error is already set
  API_RETURN_VALUE_IF_ERROR(*this); 

  //macro to set error context on -1 system error
  API_SYSTEM_CALL(path, open(path, O_RDWR));
  return *this;
}

File& File::write(const void * buf, int nbyte){
  API_RETURN_VALUE_IF_ERROR(*this); 
  API_SYSTEM_CALL("", write(fileno(), buf, nbyte));
  return *this;
}

//This code allows the following sequence
//if open fails, write will abort
char buffer[16];
if( File().open("path").write(buffer, sizeof(buffer)).is_error() ){
  // If open failed, the ErrorContext will pinpoint to open
  // If write failed, ErrorContext will show it
}
```

If you always follow rule #1, your error context will always pinpoint exactly where an error occured. Unfortunately, it isn't always where the bug is, but it gives you a good idea.

# Fix All Errors Immediately

If the code recognizes a programming error, the program should immediately stop and identify it. This is where `ASSERT` comes in to play.

```c++
void copy(void * dest, const void * source, size_t size){
  //abort the copy if there is an error
  API_RETURN_IF_ERROR();
  //copy should never be called with nullptr
  //if it was called, that is a programming error
  //fix it immediately
  ASSERT(dest != nullptr);
  ASSERT(source != nullptr);
  memcpy(dest, source, size);
}

char buffer[64];
void * memory = API_SYSTEM_CALL_NULL("no memory", malloc(64));
copy(memory, buffer, 64);

//analyze the ErrorContext to see if all is well
```

# Handling User Errors

Users errors by nature are application specific. This means that a multi-purpose C++ framework cannot address user errors. It can identify programming and execution errors, but the application code must translate that into an action the user can take to correct the problem.

For the file example above:

```c++

//This code allows the following sequence
//if open fails, write will abort
char buffer[16];
if( File().open("path").write(buffer, sizeof(buffer)).is_error() ){
  if( error_context().error_number() == ENOENT ){
    printf("%s is not a valid file\n", error_context().message());
  } else if( error_context().error_number() == ENOSPC ){
    printf("Your disk is full. Try freeing up some space\n");
  }
  reset_error();
}
```

# Why You Should Care

Error handling code can make up a substantial amount of code while adding zero value. It was taking up over 40% of my code before I changed my approach. Consider this example:

***Returning `-1` for Error***

```c++
int file_fd = open("file.txt", O_READONLY);
if( f < 0 ){ /*cascade the error up the chain*/ }

int new_file_fd = open("new_file.txt", O_APPEND | O_CREAT | O_TRUNC, 0666);
if( new_file_fd < 0 ){ /*cascade the error up the chain*/ }

char buffer[64];
int bytes_read = 0;

while( (bytes_read = read(file_fd, buffer, 64)) > 0 ){
  if( write(new_file_fd, buffer, bytes_read) < 0 ){
    //cascade the error up the chain
  }
}

if( close(file_fd) < 0){ /*cascade the error up the chain*/ }
if( close(new_file_fd) < 0){ /*cascade the error up the chain*/ }
```

***One Error Context Per Thread***

```c++
//copy file.txt -> new_file.txt
File(File::IsOverwrite::yes, "new_file.txt")
  .write(File("file.txt"));
if( api::ExecutionContext::is_error() ){
  //something didn't work -- error context has the details
}
```

Going from 10 lines of code to 3 lines of code also requires the use of [method chaining]({{< relref "2020-12-15-Method-Chaining-in-Cpp.md" >}}) and [crafty use of RAII]({{< relref "2020-12-22-RAII-Everywhere-in-Cpp.md" >}}) neither which are possible using classic error handling approaches. This case is particularly dramatic, but not rare. You can see the full [C++ framework on Github](https://github.com/StratifyLabs/API).