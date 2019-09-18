---
categories:
- Device Tips
date: "2013-10-04"
layout: post
page_source: MicrocontrollerBlog
tagline: Embedded Design
katex: true
tags:
- c/c++
- programming
- popular
title: An Easy-to-Use Digital Filter
chart: true
---

![Fourier Series](/images/316px-Fourier_Series.svg)
I want to share one of my favorite digital filters I commonly use with sensor input with microcontrollers. It is called the exponential moving average (aka EMA). It's what is known in the "biz" as an infinite impulse response filter (IIR) which means each input value affects the output for initinity. But more importantly, being an IIR filter means it only requires a small amount of RAM and computing power which is great for microcontrollers.

Before we look at my favorite filter, let's do a  hyper-brief intro to filters.

## What is a Filter?

Filters are used to remove frequencies from a signal. A long time ago, a fellow super-nerd named Joseph Fourier figured out that signals could be represented by a sum of plain old sine waves. This gave way to great mathematical tools (like the Fourier transform) that we use to understand and design filters. For example, the circuit below is a low-pass filter. It passes low frequencies and blocks high frequencies.

<center>
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="401px" height="196px" viewBox="-0.5 -0.5 401 196"><defs/><g><path d="M 269.86 160.14 L 269.86 189.86 L 20.14 189.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 269.86 160.14 L 269.86 189.86 L 380.14 189.86" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 220 110 L 265 110 M 265 80 L 265 140 M 275 110 L 320 110 M 285 80 C 271.67 97.78 271.67 122.22 285 140" fill="none" stroke="#000000" stroke-miterlimit="10" transform="rotate(90,270,110)" pointer-events="none"/><g transform="translate(221.5,109.5)rotate(90,10.5,0)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="21" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1uF</div></div></foreignObject><text x="11" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">1uF</text></switch></g><path d="M 181.29 9.86 L 269.86 9.86 L 269.86 60.14" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 81.29 9.86 L 50.43 9.86 L 20 10" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 181.29 9.86 L 280.71 9.86 L 380 10" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><path d="M 81 10 L 99 10 L 103 0 L 111 20 L 119 0 L 127 20 L 135 0 L 143 20 L 151 0 L 159 20 L 163 10 L 181 10" fill="none" stroke="#000000" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(119.5,27.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="22" height="12" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;">1kΩ</div></div></foreignObject><text x="11" y="12" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">1kΩ</text></switch></g><ellipse cx="385" cy="10" rx="5" ry="5" fill="#ffffff" stroke="#000000" pointer-events="none"/><ellipse cx="385" cy="190" rx="5" ry="5" fill="#ffffff" stroke="#000000" pointer-events="none"/><ellipse cx="15" cy="190" rx="5" ry="5" fill="#ffffff" stroke="#000000" pointer-events="none"/><ellipse cx="15" cy="10" rx="5" ry="5" fill="#ffffff" stroke="#000000" pointer-events="none"/><g transform="translate(10.5,77.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="18" height="45" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 19px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;"><div>+</div><div>V<sub>IN</sub></div><div><sub>-</sub><br /></div></div></div></foreignObject><text x="9" y="29" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g><g transform="translate(365.5,67.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="29" height="45" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; width: 30px; white-space: nowrap; overflow-wrap: normal; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;white-space:normal;"><div>+</div><div>V<sub>OUT</sub></div><div><sub>-</sub><br /></div></div></div></foreignObject><text x="15" y="29" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g></g></svg>
</center>

Analog filters are characterized by their frequency response which is how much the frequencies are attenuated (magnitude response) and shifted (phase response). For the circuit above, the magnitude response is below.

```chart
    {
    "type": "line",
        "data": {
            "labels": [
              1,
              10,
              100,
              1000,
              10000
            ],
            "datasets": [
            {
                "label": "dB (Voltage)",
                "data": [
                    0,
                    0,
                    -0.043,
                    -3.01,
                    -20.043
                    ],
                "backgroundColor":"transparent",
                "borderColor":"orange"
            }
            ]
        }
    }
```

Note that the x-axis is logarithmic (every tick mark is 10 times greater than the last one).  The y-axis is in dBV (which is a logarithmic measure of the voltage).  The "cutoff frequency" for this filter is 1000 rad/s or 160 Hz.  This is the point where less than half the power at a given frequency is transferred from the input to the output of the filter. It is also the point where the magnitude is at -3dBV (If you do the math, it works out--you'll just have to trust me for now).

The low pass filter above is great for conditioning signals to be sampled using a microcontroller ADC. The ADC only captures frequencies that are up to half the sampling frequency.  If the ADC does 320 samples per second, the signal should all be less than 160Hz. If the signal is not filtered above 160Hz, those higher frequencies will overlap onto the lower frequencies. This is a phenom known as aliasing and is a mathematically side-effect of digital sampling.

### Digital Filters

Digital filters attenuate frequencies in software rather than using analog components. Their implementation includes sampling the analog signals with an ADC then applying a software algorithm.  Two common design approaches to digital filtering are FIR filters and IIR filters.

### FIR Filters

Finite Impulse Response (FIR) filters use a finite number of samples to generate
the output.  A simple moving average is an example of a low pass FIR filter.  Higher
frequencies are attenuated because the averaging "smooths" out the signal.  The
filter is finite because the output of the filter is determined by a finite number
of input samples.  As an example, a 12 point moving average filter adds up the 12 most
recent samples then divides by 12.  The output of IIR filters is determined
by (up to) an infinite number of input samples.

### IIR Filters

Infinite Impulse Response (IIR) filters are a type of digital filter where the
output is inifinetely--in theory anyway--influenced by an input.  The exponential
moving average is an example of a low pass IIR filter.

## Exponential Moving Average Filter

An exponential moving average (EMA) applies exponential weights to each sample
in order to compute an average.  Though this seems complicated, the
equation--known in digital filtering parlance as the "difference equation"--to
compute the output is simple.  In the equation below, \\(y\\) is the output; \\(x\\) is
the input; and \\(\alpha\\) is a constant that sets the cutoff frequency.

$$ y[n] = y[n-1] \cdot (1- \alpha) + x[n] \cdot \alpha $$

The magnitude response is shown below for alpha equal 0.5 given a sampling frequency of 1000.

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
              -0.39,
              -4.77
                    ],
                "backgroundColor":"transparent",
                "borderColor":"blue"
            }
            ]
        }
    }
```

The y-axis is, again, shown in decibels.  The x-axis is logarithmic from 0.5Hz to
500Hz.

The EMA filter is beneficial in embedded designs for two reasons.  First, it is easy to adjust the cutoff frequency. Decreasing the value of alpha will lower the cutoff frequency of the filter as illustrated by comparing the above \\(\alpha = 0.5\\) plot to the below plot where \\(\alpha = 0.1\\) (pay heed to the y-axis labels).

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
              -0.18,
              -4.96,
              -12.79
                    ],
                "backgroundColor":"transparent",
                "borderColor":"blue"
            }
            ]
        }
    }
```

Second, the EMA is easy to code and requires only a small amount of computing power
and memory.  The code implementation of the filter uses the difference equation.  There
are two multiply operations and one addition operation for each output--this ignores
the operations required for rounding fixed point math.  Only the most recent sample
must be stored in RAM.  This is substantially less than using a simple moving average
filter with N points which requires N multiply and addition operations as well as N
samples to be stored in RAM.  The following code implements the EMA filter using 32-bit
fixed point math.

```c++
#define DSP_EMA_I32_ALPHA(x) ( (uint16_t)(x * 65535) )

int32_t dsp_ema_i32(
  int32_t in, 
  int32_t average, 
  uint16_t alpha
  ){
  if ( alpha == 65535 ) return in;
  int64_t tmp0;
  tmp0 = (int64_t)in * (alpha+1) + average * (65536 - alpha);
  return (int32_t)((tmp0 + 32768) / 65536);
}
```

The code below is an example of how to use the above function.

```c++
int32_t my_avg_func(void){
     static int32_t average = 0;
     int32_t adc_value;    
     adc_value = read_the_adc_value();
     average = dsp_ema_i32(
       adc_value, average, 
       DSP_EMA_I32_ALPHA(0.1)
     );
     return average;
}
```

## Conclusion

Filters, both analog and digital, are an essential part of embedded designs.  They
allow developers to get rid of unwanted frequencies when analyzing sensor input.  For
digital filters to be useful, analog filters must remove all frequencies above half
the sampling frequency.  Digital IIR filters can be powerful tools in embedded design
where resources are limited.  The exponential moving average (EMA) is an example of
such a filter that works well in embedded designs because of the low memory and
computing power requirements.
