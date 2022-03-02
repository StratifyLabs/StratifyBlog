---
categories:
- Device Tips
date: "2022-03-02"
layout: post
katex: true
chart: true
tags:
- cpp
- programming
- microcontroller
- cortex-m
title: Simple and Exponential Moving Averages in embedded C++
---


Digital filtering is a handy tool for removing noise from an ADC signal. Digital filters come in two varieties: FIR and IIR. The simple moving average and the exponential moving average are two simple examples to let you get started.

## FIR vs IIR

FIR and IIR filters differ in how much each sample affects the output. For FIR (finite impulse response), the input affects a finite number of outputs. Each input of an IIR (infinite impulse response) filter affects all subsequent output values at least in theory. In practice, the finite nature of fixed-point and floating-point math on computers means eventually an input's effect is below the rounding error.

### Difference Equations

Many times digital filters are expressed as a difference equation. The equation for a 5 sample simple moving average looks like this:

$$ y[n] = \frac{x[n] + x[n-1] + x[n-2] + x[n-3] + x[n-4]}{5} $$

That is the current output value `y[n]` is the sum of the current input value `x[n]` plus the previous 4 input values divided by 5.

The magnitude response is shown below given the sampling frequency of `1000hz`.

> It is beyond the scope of this post to show how to get the magnitude response, but in short you:
> - Find the transfer function `H(z)`
> - Evaluate the transfer funtion for {{< katex inline >}} z = e^{j \omega} {{< /katex >}}
> - Use Euler's identity {{< katex inline >}} e^{j \omega} = cos(\omega) +  j \cdot sin(\omega) {{< /katex >}} to evalute the magnitude of the complex number where {{< katex inline >}} \omega = 2 \pi F_s {{< /katex >}}

```chart
    {
    "type": "line",
        "data": {
            "labels": [
              0.5,
              5,
              50,
              500
            ],
            "datasets": [
             {
                "label": "dB",
                "data": [
              0,
              0,
              -0.43,
              -6.9897
                    ],
                "backgroundColor":"transparent",
                "borderColor":"blue"
            }
            ]
        }
    }
```

The exponential moving average equation is:

$$ y[n] = y[n-1] \cdot (1- \alpha) + x[n] \cdot \alpha $$

The range for {{< katex inline >}} \alpha {{< /katex >}} value is greater than zero to one (output is assigned to the input). This is an IIR filter because the output is dependent upon a previous output value {{< katex inline >}} y[n-1] {{< /katex >}}. You will notice the simple moving average is only dependent upon previous input values.


The magnitude response is shown below for alpha equal to `0.5` given a sampling frequency of `1000hz`.

```chart
    {
    "type": "line",
        "data": {
            "labels": [
              0.5,
              5,
              50,
              500
            ],
            "datasets": [
             {
                "label": "dB alpha=0.5",
                "data": [
              0,
              0,
              -0.39,
              -4.77
                    ],
                "backgroundColor":"transparent",
                "borderColor":"blue"
            },
            {
              "label": "dB alpha=0.1",
                "data": [
              0,
              0,
              -5.79,
              -11.15
                    ],
                "backgroundColor":"transparent",
                "borderColor":"red"
            }
            ]
        }
    }
```

The frequency response of the IIR filter can be changed simply by adjusting the value of {{< katex inline >}} \alpha {{< /katex >}}. For the FIR filter, you need to add more samples.

## Simple Moving Average

The code for the simple moving average is a little more complex than the IIR. We are going to use an optimized version that minimizes the number of operations. To do this, we start with a ring buffer that allows us to easily access the first and last values in the buffer which is what we will need to keep a sum of the buffer contents.

This implementation uses template metaprogramming (denoted by the use of the `template` keyword). This makes it easy to implement simple moving averages of both different tap sizes and number types.

```cpp
template <typename Type, size_t Count>
class SimpleRingBuffer {
public:
  SimpleRingBuffer() { flush(); }

  constexpr size_t count() const { return Count; }

  SimpleRingBuffer &push(Type value) {
    m_buffer[m_head] = value;
    m_head++;
    if (m_head == Count) {
      m_head = 0;
    }
    return *this;
  }

  Type back() const { return m_buffer[m_head]; }
  Type front() const { return at(0); }

  Type at(size_t offset) {
    const auto adjusted_offset
      = m_head > offset ? m_head - offset - 1 : Count + m_head - offset - 1;
    return m_buffer[adjusted_offset];
  }

  SimpleRingBuffer &flush() {
    for (auto &value : m_buffer) {
      value = {};
    }
    m_head = 0;
    return *this;
  }

private:
  size_t m_head = 0;
  var::Array<Type, Count> m_buffer;
};
```

And here is our `SimpleMovingAverage` class:

```cpp
template <typename Type, size_t Count>
class SimpleMovingAverage {
public:
  constexpr size_t count() const { return Count; }

  SimpleMovingAverage &calculate(Type input) {
    sum += input;
    sum -= m_buffer.back();
    m_buffer.push(input);
    return *this;
  }

  Type present_value() const { return sum / Count; }

private:
  SimpleRingBuffer<Type, Count> m_buffer;
  Type sum = {};
};
```

Now let's show how to use this filter:

```cpp

int main(){
  {
    SimpleMovingAverage<float, 5> filter;
    for(int i=0; i < 1000; i++){
      const auto input = read_adc_float();
      if( i < filter.count() ){
        //the filter output won't be valid until the sum buffer is full
        filter.calculate(input);
      } else {
        const auto value = filter.calculate(input).present_value();
        printf("Filter value is %0.3f\n", value);
      }
    }
  }

  {
    //It is easy to change the filter taps/type
    SimpleMovingAverage<u32, 16> another_filter;
    for(int i=0; i < 1000; i++){
      const auto input = read_adc_u32();
      if( i < another_filter.count() ){
        //the filter output won't be valid until the sum buffer is full
        another_filter.calculate(input);
      } else {
        const auto value = another_filter.calculate(input).present_value();
        printf("Filter value is %u\n", value);
      }
    }
  }

}
```

> A note on template metaprogramming. Every time you declare `SimpleMovingAverage` with different template parameters, the compiler will generate unique functions for each different declaration. To keep your code size small, limit the number of unique filters you use. For example, pick a standard tap size and number type if it will work.

## Exponential Moving Average

Our exponential moving average is simpler to implement. Again, we use template metaprogramming to make it easier to declare a filter of different types.

```cpp
template <typename Small, typename Medium, typename Large>
class LowPassFilter {
public:
  LowPassFilter(Medium start) : m_present_value(start){}
  static constexpr Medium small_max() { return 1 << (8 * sizeof(Small)); }

  LowPassFilter &set_alpha(Small value) {
    m_alpha = value;
    return *this;
  }

  LowPassFilter &calculate(Medium in) {
    //this needs to be carefully coded to ensure the
    //integer math doesn't have any overflow issues
    intlarge tmp0;
    tmp0 = (Large)in * (m_alpha) +
           (Large)m_present_value * (small_max() - m_alpha);
    m_present_value = (Medium)(((Large)tmp0 + (Large)small_max()) >>
                            (sizeof(Small) * 8));
    return *this;
  }

  LowPassFilter &reset(Medium v) {
    m_present_value = v;
    return *this;
  }

  Medium present_value() const { return m_present_value; }

private:
  Medium m_present_value;
  Small m_alpha;
};

```

The `float/double` version is less complex because we don't have to worry about optimizing the integer math.

```cpp
template<typename Type> class LowPassFilterFloat {
public:
  LowPassFilterFloat(Type start = {});

  LowPassFilterFloat &set_alpha(Type value) {
    m_alpha = value;
    return *this;
  }

  float calculate_alpha(Type sampling_frequency, Type magnitude = 0.5f);

  LowPassFilterFloat &reset(Type start){
    m_present_value = start;
    return *this;
  }

  LowPassFilterFloat &calculate(Type in){
    m_present_value = in * (m_alpha) + m_present_value * (1.0f - m_alpha);
    return *this;
  }

private:
  Type m_alpha;
};
```

Now let's look at how these can be used:

```cpp

int main(){
  {
    //this will let us filter up to 16-bit values coming in to the
    //ADC using integer math
    LowPassFilter<u8, u16, u32> filter;
    filter.set_alpha(128); //alpha is from 0 to 255 based on the Small type (u8)
    for(int i=0; i < 1000; i++){
      const auto input = read_adc_u16();
      if (i == 0 ){
        //set the filter to the first value to speed up the settling process
        filter.reset(input);
      }

      const auto value = filter.calculate(input).present_value();
      printf("Filter value is %u\n", value);
    }
  }

  {
    //It is easy to change the filter taps/type
    LowPassFilterFloat<float> another_filter;
    for(int i=0; i < 1000; i++){
      const auto input = read_adc_float();
      if (i == 0 ){
        //set the filter to the first value to speed up the settling process
        another_filter.reset(input);
      }

      const auto value = another_filter.calculate(input).present_value();
      printf("Filter value is %0.3f\n", value);
    }
  }

}
```

## The End

We looked at two types of low pass filters one having an FIR and the other an IIR. The code above was extracted from this [repository](https://github.com/StratifyLabs/DspAPI) which contains a couple more filter types. If you learn about a few different filters (IIR high pass, Biquad), you can use this approach to have an excellent toolbox for manipulating all types of signals.
