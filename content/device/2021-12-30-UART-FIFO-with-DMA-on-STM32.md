---
categories:
- Device Tips
date: "2021-12-27"
layout: post
draft: false
tags:
- cpp
- programming
- microcontroller
- cortex-m
title: UART FIFO with DMA on STM32
---

Configuring the STM32 UART for high throughput data can be challenging. Most STM32 peripherals rely on DMA for high throughput, such as I2S for digital audio streaming.

## I2S DMA Operation

Implementing DMA for I2S to receive digital audio is straightforward. You configure the DMA to match the audio format and choose a frame buffer size. Then, you configure the I2S in circular buffer mode. The STM32 interrupts when the frame buffer is half-full and full. You use the processor to access the half of the buffer being filled by the DMA and vice-versa.

## The Problem with UART DMA

But using the DMA with the UART is weird. It contrasts with the I2S:

| I2S                       | UART                        |
| ------------------------- | --------------------------- |
| Fixed Packet Size         | Variable Packet Size        |
| Known Packet Size         | Unknown Packet Size         |
| Predictable Arrival Times | Unpredictable Arrival Times |

There is no built-in way to configure the UART for this situation, but there is at least one solution that works well.

## The Works Great Solution

To create a complete solution, we need to add a software FIFO buffer and an idle interrupt to a standard UART DMA circular buffer configuration.

### The FIFO Buffer

> If you need more background info on FIFOs, check this out: [A FIFO Buffer Implementation]({{< relref "2013-10-02-A-FIFO-Buffer-Implementation.md" >}}).

FIFO means first-in, first-out. In this case, the DMA reads data from the UART and writes it to the FIFO buffer. The DMA is configured in circular mode with the half-complete and complete events being serviced.

I like to use the `head`/`tail` terminology to describe how a FIFO works. Data is written to the FIFO at the `head` and read at the `tail`. The ISR routines moves the `head` (and `tail` as needed to discard unread data). The user software moves the `tail`. If the `head` and `tail` are in the same place, the FIFO is empty.

![Empty FIFO](/images/uart-dma-fifo-empty.svg)

Once a byte arrives on the DMA, it is written to the first location of the FIFO. The DMA does not know that it is writing to a FIFO, it is simply writing to a circular buffer in the same way that it would with any other implementation. The next byte is written to the second location in the FIFO, and the next byte is written to the third location, and so on.

![3 Bytes Unserviced](/images/uart-dma-fifo-3-bytes-unserviced.svg)

At this point, if the software tried to read the FIFO, it would determine that it is empty because `head == tail`. The `head` value stays on zero until one of these happens:

- A idle interrupt
- A half-complete interrupt
- A complete interrupt

When the line goes idle, the ISR will fire and update the value for `head`.

```c++
//code used to increment the head and discard old data
//by advancing the tail
void advance_head(int bytes_received){
  while(bytes_received--){
    head++;
    if( head == fifo_size ){
      head = 0;
    }
    if( head == tail ){
      //this will discard the oldest data
      tail++;
      if( tail == fifo_size ){
        tail = 0;
      }
    }
  }
}

void HAL_UART_RxIdleCallback(UART_HandleTypeDef *huart) {
  bytes_received = 
    fifo_size - 
    __HAL_DMA_GET_COUNTER(huart->hdmarx) 
    - head;
  advance_head(bytes_received);
}
```

![3 Bytes](/images/uart-dma-fifo-3-bytes.svg)


The user can now read the three bytes that arrived.

```c++
//you don't want any UART interrupts happening during this time
//also make sure output buffer is >= fifo_size or limit the
//number of bytes read
void read_uart(char * output){
  while(tail != head){
    *(output++) = buffer[tail];
    tail++;
    if( tail == fifo_size ){
      tail = 0;
    }
  }
}
```

![3 Bytes Serviced FIFO](/images/uart-dma-fifo-3-bytes-serviced.svg)

When the DMA half-complete interrupt is serviced, it always sets the head to `fifo_size/2`. It advances the `head` and `tail` just like the idle interrupt does.

```c++
void HAL_UART_RxHalfCpltCallback(UART_HandleTypeDef *huart) {
  bytes_received = 
    fifo_size/2
    - head;

  advance_head(bytes_received);
}
```

![Half Full FIFO](/images/uart-dma-fifo-half.svg)

Similarly, the `head` will point to zero after the transfer-complete interrupt fires. If the user software hasn't read the data, it will be discarded by advancing the `tail`.

```c++
void HAL_UART_RxCpltCallback(UART_HandleTypeDef *huart) {
  bytes_received = 
    fifo_size
    - head;

  advance_head(bytes_received);
}
```

![Full FIFO](/images/uart-dma-fifo-full.svg)


## The End

The UART DMA implementation on STM32 MCUs can be tricky, but using a FIFO buffer and the idle interrupt allows it to work well. That's the basic idea.

> You can find a full code example [here](https://github.com/StratifyLabs/StratifyOS-mcu-stm32/blob/main/src/uart/uart_local.c).




