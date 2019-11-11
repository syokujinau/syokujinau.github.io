---
title: Signal Statistics and CMSIS-DSP
date: 2019-09-12 14:14:10
tags:
  - DSP
  - STM32 
  - ARM
thumbnail: https://i.imgur.com/VFOqYdF.png
---


## Mean and Standard Deviation

* Mead 平均數
    * <img src="https://render.githubusercontent.com/render/math?math=\LARGE  \mu = \frac{1}{N}\sum^{N-1}_{i=0}x_i = \frac{x_0 + x_1 + \dots + x_{N-1}}{N}" />
    * 物理意義: DC signal (Direct current) 直流分量
* Deviation
    * <img src="https://render.githubusercontent.com/render/math?math=\large i" />  deviates from the mean: <img src="https://render.githubusercontent.com/render/math?math=\large |x_i - \mu|" />
    * Average deviation: <img src="https://render.githubusercontent.com/render/math?math=\LARGE \frac{1}{N-1}\sum^{N-1}_{i=0}|x_i - \mu|" /> 
    * 物理意義: 訊號樣本點與平均之距離
* Standard Deviation 標準差
    * <img src="https://render.githubusercontent.com/render/math?math=\large {\color{Red} P} \propto {\color{Orange} V^{ {\color{Orange}2} } }" /> 
    * <img src="https://render.githubusercontent.com/render/math?math=\LARGE {\color{Red} \sigma} = R.M.S = \sqrt{\frac{1}{N-1}\sum^{N-1}_{i=0}(x_i - {\color{Orange} \mu})^{\color{Orange} 2}}" /> 
    * 物理意義: 顯示單一個測量值(電壓)與平均值間可能偏差的程度(功率)
* Variance 變異數
    * <img src="https://render.githubusercontent.com/render/math?math=\LARGE {\color{Red} \sigma^{\color{Red}2}}$ = $\frac{1}{N-1}\sum^{N-1}_{i=0}(x_i - {\color{Orange} \mu})^{\color{Orange} 2}" />
    * 物理意義: 描述訊號的波動範圍，表示訊號中交流分量的強弱，即交流訊號的平均功率
* Signal ro Noise ratio (SNR)
    * <img src="https://render.githubusercontent.com/render/math?math=\LARGE SNR = \frac{\mu}{\sigma}" /> 
* Coefficient of Variation
    * <img src="https://render.githubusercontent.com/render/math?math=\LARGE CV = \frac{\mu}{\sigma} \times 100" /> 
<!-- more -->

### Implementation

```c
float32_t signal_mean(float32_t *signal_src, uint32_t signal_length) {
    float32_t mean = 0.0;
    uint32_t i;
    for(i = 0; i < signal_length; i++) {
        mean = mean + signal_src[i];
    }
    mean /= (float32_t)signal_length;
    
    return mean;
}
```

```c
float32_t signal_variance(float32_t *signal_src, uint32_t signal_length, float32_t signal_mean) {
    
    float32_t variance = 0.0;
    uint32_t i;
    for(i = 0; i < signal_length; i++) {
        variance = variance + pow((signal_src[i] - signal_mean), 2);
    }
    variance /= (signal_length - 1);
    
    return variance;
}
```

```c
float32_t signal_standard_deviation (float32_t signal_variance) {
    float32_t std = sqrtf(signal_variance);
    
    return std;
}
```


## CMSIS-DSP

CMSIS為DSP應用處理數值(多使用`float32_t`等浮點數)，並最佳化，例如用迴圈做320樣本點`float32_t`訊號做平均數計算常常無法算出，使用函式庫則能做高效率的運算。包含以下幾種類別的運算
* Basic math
* Filter 
* Thransform
* Interpolation
* Complex math
* Control (PID)
* Fast math 
* Statistical
* Matrix
* Support function (min. max, ...)

### Data type

1. Signed integers
    * `int8_t`, `int16_t`, `int32_t`, `int64_t`
2. Unsigned integers
    * `uint8_t`, `uint16_t`, `uint32_t`, `uint64_t`
3. Floating point
    * `float32_t`, `float64_t`
4. Fraction
    * `q7_t` (8-bit), `q15_t` (16-bit), `q31_t` (32-bit), `q63_t` (64-bit)


### ARM Math APIs

* `arm_mean_f32`
* `arm_var_f32`
* `arm_std_f32`


## ADC

ADC consists of **Sample/Hold** and **Quantizer** to convert analog to digital.
* Sample/Hold: convert independent variable of signal from continuous to discrete 
* Quantizer: convert dependent variable of signal from continuous to discrete

### Sampling theorem

Reconstructing original analog signal from sample points, the sample rate must satisfy <img src="https://render.githubusercontent.com/render/math?math=\large f_s \geq 2f_{max}" /> , also called Nyquist theorem.





## RC Passive Filter

> Filter circuit is created by passive components such as capacitor, inductor and resistor.

* Design RC value
   * 大川電子設計 online tool: http://sim.okawa-denshi.jp/en/Fkeisan.htm
* Capacitor
    * Short circuit in high freq
    * Open circuit in low freq
* Cut-off frequency 截止頻率
    * <img src="https://render.githubusercontent.com/render/math?math=\large f_{c} = \frac{1}{2 \pi RC}" />



#### Low pass 

![](https://i.imgur.com/x78F13J.png)




#### High pass 

![](https://i.imgur.com/nBDOCG1.png)


### Sallen-Key High-pass Filter 

![](https://i.imgur.com/huHZH9t.png)


(source: http://sim.okawa-denshi.jp/)
* Details: https://www.electronics-tutorials.ws/filter/sallen-key-filter.html
* More components (both passive and active components) means ploes and zeros, could achieve better performance.

## Analog filter

Three common filter used for DSP application

1. Bessel filter
    * Better step response
2. Chebyshev filter
    * Better frequency response
3. Butterworth filter
