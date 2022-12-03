---
categories:
- Device Tips
date: "2013-10-02"
layout: post
page_source: MicrocontrollerBlog
tagline: Embedded Design
tags:
- microcontroller
- programming
- cpp
- popular
title: A FIFO Buffer Implementation
---

![FIFO](/images/fifo.svg)
A FIFO buffer is a useful way to store data that arrives at a microcontroller peripheral asynchronously but cannot be read immediately. An example of this is storing bytes that are incoming on a UART. Buffering the bytes can make it easier for the embedded firmware to handle the incoming data in real time.

A FIFO buffer is a type of data storage that operates on a first-in, first-out basis. It typically uses an array of contiguous memory to store data. Data is written to the "head" of the buffer and read from the "tail." When the head or tail reaches the end of the memory array, it wraps around to the beginning. If the tail runs into the head, the buffer is empty. If the head runs into the tail, the buffer is full, and the implementation must determine whether the oldest data is discarded or the write is not completed. In the example below, the write is not completed.


## General Implementation

The code below shows a general implementation of a FIFO buffer. It assumes that an overflow condition is not allowed, meaning that when the head meets the tail, the buffer is full and no more data can be written.

```c++
typedef struct {
     char * buf;
     int head;
     int tail;
     int size;
} fifo_t;

//This initializes the FIFO structure with the given buffer and size
void fifo_init(fifo_t * f, char * buf, int size){
     f->head = 0;
     f->tail = 0;
     f->size = size;
     f->buf = buf;
}

//This reads nbytes bytes from the FIFO
//The number of bytes read is returned
int fifo_read(fifo_t * f, void * buf, int nbytes){
     int i;
     char * p;
     p = buf;
     for(i=0; i < nbytes; i++){
          if( f->tail != f->head ){ //see if any data is available
               *p++ = f->buf[f->tail];  //grab a byte from the buffer
               f->tail++;  //increment the tail
               if( f->tail == f->size ){  //check for wrap-around
                    f->tail = 0;
               }
          } else {
               return i; //number of bytes read
          }
     }
     return nbytes;
}

//This writes up to nbytes bytes to the FIFO
//If the head runs in to the tail, not all bytes are written
//The number of bytes written is returned
int fifo_write(fifo_t * f, const void * buf, int nbytes){
     int i;
     const char * p;
     p = buf;
     for(i=0; i < nbytes; i++){
           //first check to see if there is space in the buffer
           if( (f->head + 1 == f->tail) ||
                ( (f->head + 1 == f->size) && (f->tail == 0) ){
                 return i; //no more room
           } else {
               f->buf[f->head] = *p++;
               f->head++;  //increment the head
               if( f->head == f->size ){  //check for wrap-around
                    f->head = 0;
               }
           }
     }
     return nbytes;
}
```

## Using a FIFO with a Microcontroller UART

Many microcontroller designs have limited buffer space for data arriving on the UART. Using a FIFO in the UART ISR can make it easier to manage incoming data. The following pseudo-code uses the FIFO implementation above as part of the UART ISR.


> FIFOs are simple and effective mechanisms for managing data in real-time systems. The example given above can be easily adapted to work with I2S by using frames instead of bytes. For example, the I2S frame can be defined as 32 bytes in 1ms, and when the frame is full, the head points to the next frame. This way, the FIFO can manage the arrival of data from the I2S peripheral and make it easier for the application to process the data in real time. A C implementation of a framed FIFO is available in <a href="https://github.com/StratifyLabs/StratifyOS/blob/master/src/device/ffifo.c" target="_blank"><b>Stratify OS</b></a>.

```c++
static fifo_t * uart_fifo;

void uart_fifo_init(fifo_t * fifo){
     uart_fifo = fifo;
}

void uart_isr(void){
     char incoming_byte;
     //execute any required ISR entrance code

     while( UART_REGISTER_FLAG_DATA_READY ){ //check the register for incoming data
          incoming_byte = UART_REGISTER_READ_DATA_BYTE; //read the UART data
          fifo_write(uart_fifo, &amp;incoming_byte, 1); //write the data to the fifo
     }

     //execute any required ISR exit code
}
```  

Using a FIFO as described above can reduce the real-time requirements for an application, as well as give the application developer more flexibility in handling incoming data. Because the FIFO buffer stores the data until it can be processed by the application, the application does not need to handle each byte as it arrives. This can make it easier to develop real-time applications that must process large amounts of data.

## Conclusion

FIFOs are a type of data storage that operate on a first-in, first-out basis. They are useful for managing the arrival of asynchronous data. By integrating a FIFO with an interrupt service routine (ISR) like the UART ISR, an application developer can more easily process incoming data. FIFOs can help to reduce the real-time requirements of an application and provide more flexibility in handling incoming data.
