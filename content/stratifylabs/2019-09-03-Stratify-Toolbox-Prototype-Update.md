---
categories:
- Stratify Labs
date: "2019-09-03"
layout: post
tags: [business, product development, stratifylabs]
title: Stratify Toolbox Prototype Update
---

> This is an updating post for Stratify Toolbox prototype development updates. Come back soon to see how the progress is going.

## Update 2019-09-03

![Stratify Toolbox Prototype](/images/toolbox-proto-web.png)


The Stratify Toolbox prototype is really starting to come together. The firmware development is ongoing but the following highlights parts that are up and running.

- STM32H750 microcontroller
  - Running at 416MHz
  - Enough RAM to run a handful of powerful applications concurrently
- External 8MB Flash drive
  - Allows applications to easily save and retrieve user data
  - Fast QSPI connection
- 320 x 240 Pixel IPS Display
  - The Display looks great (check out the image above)
  - IPS has excellent viewing angles

One of the big challenges in getting the display working was getting the STM32 FMC to work in 8-bit parallel mode. The problem I ran in to was that every transaction was a 64-bit one.

I dug in to this and found that the AXI bus is 64-bits. And it would write extra bytes if the memory width (8-bit parallel interface) was smaller than 64-bits. This meant 8 bytes were written every time I tried to access the bus.

I partly solved the problem by telling the STM32 FMC driver the bus width was 32-bits instead of 8-bits. This meant that the top 24-bits were ignored because those pins weren't connected to the FMC (just the lowest 8-bits). But now, every transaction was 2 bytes instead of one.

The final step was to plan every register and data write based on the fact that 2 bytes were written everytime. Writing command zero is a NOP so I just sent a NOP before every command. Then I padded the data with a zero byte if needed to complete the transaction.

```c++

typedef struct MCU_PACK {
	u8 first;
	u8 dummy[3];
	u8 second;
	u8 dummy0[3];
} transaction_data_t;

typedef union {
	u64 raw;
	transaction_data_t data;
} transaction_t;

void write_command(u8 Reg){
	SET_COMMAND();
	cortexm_delay_us(2);
	transaction_t transaction;
	transaction.data.first = 0; //NOP
	transaction.data.second = Reg; //register value
	*m_reg_location = transaction.raw;
	__DSB();
	cortexm_delay_us(1);
	SET_DATA();
	cortexm_delay_us(1);
}

void write_data_block(const u8 * data, int nbyte){
	transaction_t transaction;
	for(u32 i=0; i < nbyte; i+=2 ){
		transaction.data.first = data[i];
		transaction.data.second = data[i+1];
		*m_reg_location = transaction.raw;
		__DSB();
	}
}
```









