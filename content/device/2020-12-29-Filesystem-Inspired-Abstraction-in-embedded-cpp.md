---
categories:
- Device Tips
date: "2020-12-29"
layout: post
tags:
- microcontroller
- cpp
- programming
title: Filesystem Inspired Abstraction in Embedded C++ 
---

I recently rewrote a family of C++ libraries that I had been using for 10 years. The main themes of the rewrite include:

- [Thread Local Error Contexts]({{< relref "2020-12-14-Thread-Local-Error-Context-in-Cpp.md" >}})
- [Method Chaining]({{< relref "2020-12-15-Method-Chaining-in-Cpp.md" >}})
- [Strong Arguments]({{< relref "2019-08-16-Using-Strong-Type-Arguments-on-Embedded-Cpp.md">}})
- [RAII Everywhere]({{< relref "2020-12-22-RAII-Everywhere-in-Cpp.md" >}})
- **Filesystems Inspired Abstraction**

## Why Filesystems?

I became quite familiar the power of filesystems when writing [Stratify OS](https://github.com/StratifyLabs/StratifyOS). Stratify OS is a POSIX based operating system for microcontrollers. POSIX was born out of a need to [standardize early Unix-like operating systems](https://en.wikipedia.org/wiki/POSIX). One of the core features of UNIX is treating many different constructs as files. In Unix, files, network sockets, and devices can all be read/written using the same API. As I was rethinking my C++ libraries, the idea of treating even more constructs like fails made complete sense. In the [API framework](https://github.com/StratifyLabs/API), I extended the idea of file-like interaction to heap memory, a variable on the stack, or even callbacks. Doing this creates a universal API for data management regardless of the medium.

## A Buffered Approach

The [API framework](https://github.com/StratifyLabs/API) is primarily designed to run on memory constrained systems (but can be used on anything POSIX compatible, including Windows). On memory constrained systems, there is a constant need to do operations with limited buffers. For example, if I want to write a large file to a socket, I can't just load the whole file in RAM then send it. I need to load manageable chunks into RAM and send those chunks before reading more. The code looks something like this (just for illustration, not-compiled):

```c++
int write_file_to_socket(int file_fd, int socket_fd, int file_size){
  //reasonable buffer
  char buffer[512];
  int bytes_transferred = 0;
  int page_size;

  do {
    page_size = (file_size - bytes_transferred > 512) ? 512 : file_size - bytes_transferred;
    int result = read(file_fd, buffer, page_size);
    if( result > 0 ){
      write(socket_fd, buffer, result);
      bytes_transferred += result;
    } else {
      return bytes_transferred;
    }

  } while( bytes_transferred < file_size);

  return bytes_transferred;
}
```

The above code (or variations of it) seemed to appear *a lot* in my previous C++ libraries. To avoid rewriting this algorithm, I created an abstrat `FileObject` that could be written to by reading another `FileObject`.

```c++
class Write {
  //including things like buffer size, max size, file offset
  //to give flexibility in doing the transfer
};
FileObject& write(const FileObject & source, const Write& options = Write()){
  //basically the above but with some flexible options for page_size
  //and buffer_size
  return *this;
}
```

Ultimately, this meant I could write a file to a socket like this:

```c++
Socket socket; //inherits FileObject
File file; //inherits FileObject
socket.write(file, File::Write().set_page_size(256));
```

Using [RAII]({{< relref "2020-12-22-RAII-Everywhere-in-Cpp.md" >}}) to open/close files, you can copy a file from one location to another like this:

```c++
File(File::IsOverwrite::yes, "dest.txt").write(File("source.txt"));
```

## All Kinds of Files

By adding a `DataFile` class that uses the heap to create a file in memory, you can read a socket:

```c++
Socket socket;
DataFile incoming; //inherits FileObject
incoming.write(socket);
```

A `ViewFile` turns any variable into a file:

```c++
typedef struct {
  int version;
  int size;
} header_t;

header_t header;
//read the file header into header
ViewFile(View(header)).write(File("file_with_header.dat");
```
A `LambaFile` allows you to provide read/write callbacks which is handy if the data read from a file requires some complex manipulation rather than just transfer to another device.

## Devices are also Files

Of course, devices are also implemented as files.

```c++
//create a uart log file -- blocks until 2048 bytes have been written
File(File::IsOverwrite::yes, "uart_log.txt").write(Uart("/dev/uart0"), File::Write().set_size(2048));
```

## What Now?

If you write C/C++ code on constrained devices, you are certainly familiar with the buffered data transfer example above. Inheritance in C++ is a powerful tool. With the right approach, you can re-use the same code in many, many different situations. Feel-free to use file-like abstractions or create your own to maximize code re-use.