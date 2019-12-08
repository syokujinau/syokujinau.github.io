---
title: Setup 100MHz HCLK and ST-LINK debugger for logic analyzer of Keil IDE
date: 2019-09-11 17:04:10
tags:
- DSP
- STM32 
- ARM
categories:
- [Embedded, ARM]
thumbnail: https://i.imgur.com/okSiQ42.png
---
<!-- <img src="https://render.githubusercontent.com/render/math?math=e^{i \pi} = -1"> -->
# FIR fliter 

> This project will go through the setup steps for DSP application on ARM CortexM4 processor  

## Algorithm
The FIR filter algorithm is based upon a sequence of multiply-accumulate (MAC) operations. Each filter coefficient $b[n]$ is multiplied by a state variable which equals a previous input sample $x[n]$.


<img src="https://render.githubusercontent.com/render/math?math=y[n] = b[0] * x[n] + b[1] * x[n-1] + b[2] * x[n-2] + ...+ b[numTaps-1] * x[n-numTaps+1]">

<!-- more -->
![](https://i.imgur.com/dUuvgc1.png)

## Finite Impulse Response filter

pCoeffs points to a coefficient array of size numTaps. Coefficients are stored in time reversed order.
    $${b[numTaps-1], b[numTaps-2], b[N-2], ..., b[1], b[0]}$$
pState points to a state array of size numTaps + blockSize - 1. Samples in the state buffer are stored in the following order.
    $${x[n-numTaps+1], x[n-numTaps], x[n-numTaps-1], x[n-numTaps-2]....x[0], x[1], ..., x[blockSize-1]}$$
Note that the length of the state buffer exceeds the length of the coefficient array by blockSize-1. The increased state buffer length allows circular addressing, which is traditionally used in the FIR filters, to be avoided and yields a significant speed improvement. The state variables are updated after each block of data is processed; the coefficients are untouched.

Finite Impulse Response filter

## 1. New Project and Logic Analyzer


1. Select Libraries
![](https://i.imgur.com/GJMatKW.png)
2. Add `main.c` to project
![](https://i.imgur.com/LH5k6Qm.png)
5. Set frequency
![](https://i.imgur.com/JEaWJOM.png)
3. Setup debugger
![](https://i.imgur.com/LR3xEpc.png)
4. Check default frequency

```c
#include "stm32f4xx_hal.h"              // Keil::Device:STM32Cube HAL:Common

uint32_t freq; // In debug mode, add this variable to watch window

int main () {
    
    HAL_Init();
    
    freq = HAL_RCC_GetHCLKFreq();
    
    while(1) {
            
    }
}

void SysTick_Handler(void) {
    HAL_IncTick();
    HAL_SYSTICK_IRQHandler();
}


```

![](https://i.imgur.com/LtLM5Gy.png)

6. Set trace core clock
![](https://i.imgur.com/1ZDwwYB.png)


7. Test Logic analyzer
```c
#include "stm32f4xx_hal.h"              // Keil::Device:STM32Cube HAL:Common

uint32_t freq;

uint32_t counter = 50;

int main () {
    
    //int i;
    
    HAL_Init();
    freq = HAL_RCC_GetHCLKFreq();
    
    while(1) {
    counter++;
    if(counter == 100) counter = 0;
        
        HAL_Delay(10);
    }
}

void SysTick_Handler(void) {
    HAL_IncTick();
    HAL_SYSTICK_IRQHandler();
}


```

![](https://i.imgur.com/E0xmZc1.png)

### Set clock

1. Use CubeMX, set HCLK to 100MHz
![](https://i.imgur.com/tGrNPkl.png)
2. Copy the `SystemClock_Config` code

```c
#include "stm32f4xx_hal.h"              // Keil::Device:STM32Cube HAL:Common

void _Error_Handler(char *file, int line);

void SystemClock_Config(void)
{

  RCC_OscInitTypeDef RCC_OscInitStruct;
  RCC_ClkInitTypeDef RCC_ClkInitStruct;

    /**Configure the main internal regulator output voltage 
    */
  __HAL_RCC_PWR_CLK_ENABLE();

  __HAL_PWR_VOLTAGESCALING_CONFIG(PWR_REGULATOR_VOLTAGE_SCALE1);

    /**Initializes the CPU, AHB and APB busses clocks 
    */
  RCC_OscInitStruct.OscillatorType = RCC_OSCILLATORTYPE_HSI;
  RCC_OscInitStruct.HSIState = RCC_HSI_ON;
  RCC_OscInitStruct.HSICalibrationValue = 16;
  RCC_OscInitStruct.PLL.PLLState = RCC_PLL_ON;
  RCC_OscInitStruct.PLL.PLLSource = RCC_PLLSOURCE_HSI;
  RCC_OscInitStruct.PLL.PLLM = 8;
  RCC_OscInitStruct.PLL.PLLN = 100;
  RCC_OscInitStruct.PLL.PLLP = RCC_PLLP_DIV2;
  RCC_OscInitStruct.PLL.PLLQ = 4;
  if (HAL_RCC_OscConfig(&RCC_OscInitStruct) != HAL_OK)
  {
    _Error_Handler(__FILE__, __LINE__);
  }

    /**Initializes the CPU, AHB and APB busses clocks 
    */
  RCC_ClkInitStruct.ClockType = RCC_CLOCKTYPE_HCLK|RCC_CLOCKTYPE_SYSCLK
                              |RCC_CLOCKTYPE_PCLK1|RCC_CLOCKTYPE_PCLK2;
  RCC_ClkInitStruct.SYSCLKSource = RCC_SYSCLKSOURCE_PLLCLK;
  RCC_ClkInitStruct.AHBCLKDivider = RCC_SYSCLK_DIV1;
  RCC_ClkInitStruct.APB1CLKDivider = RCC_HCLK_DIV2;
  RCC_ClkInitStruct.APB2CLKDivider = RCC_HCLK_DIV1;

  if (HAL_RCC_ClockConfig(&RCC_ClkInitStruct, FLASH_LATENCY_3) != HAL_OK)
  {
    _Error_Handler(__FILE__, __LINE__);
  }

    /**Configure the Systick interrupt time 
    */
  HAL_SYSTICK_Config(HAL_RCC_GetHCLKFreq()/1000);

    /**Configure the Systick 
    */
  HAL_SYSTICK_CLKSourceConfig(SYSTICK_CLKSOURCE_HCLK);

  /* SysTick_IRQn interrupt configuration */
  HAL_NVIC_SetPriority(SysTick_IRQn, 0, 0);
}

/* USER CODE BEGIN 4 */

/* USER CODE END 4 */

/**
  * @brief  This function is executed in case of error occurrence.
  * @param  file: The file name as string.
  * @param  line: The line in file as a number.
  * @retval None
  */
void _Error_Handler(char *file, int line)
{
  /* USER CODE BEGIN Error_Handler_Debug */
  /* User can add his own implementation to report the HAL error return state */
  while(1)
  {
  }
  /* USER CODE END Error_Handler_Debug */
}
```
3. Add `clock.c` and config function
![](https://i.imgur.com/lUHQ35A.png)
4. Check variable in watch window
![](https://i.imgur.com/LLWIjwo.png)


## 2. Generate sin wave and plot

Plot the wave below 

```c
#include "arm_math.h"                   // ARM::CMSIS:DSP


float32_t inputSignal_f32_1kHz_15kHz[320] =
{
+0.0000000000f, +0.5924659585f, -0.0947343455f, +0.1913417162f, +1.0000000000f, +0.4174197128f, +0.3535533906f, +1.2552931065f, 
+0.8660254038f, +0.4619397663f, +1.3194792169f, +1.1827865776f, +0.5000000000f, +1.1827865776f, +1.3194792169f, +0.4619397663f, 
+0.8660254038f, +1.2552931065f, +0.3535533906f, +0.4174197128f, +1.0000000000f, +0.1913417162f, -0.0947343455f, +0.5924659585f, 
-0.0000000000f, -0.5924659585f, +0.0947343455f, -0.1913417162f, -1.0000000000f, -0.4174197128f, -0.3535533906f, -1.2552931065f, 
-0.8660254038f, -0.4619397663f, -1.3194792169f, -1.1827865776f, -0.5000000000f, -1.1827865776f, -1.3194792169f, -0.4619397663f, 
-0.8660254038f, -1.2552931065f, -0.3535533906f, -0.4174197128f, -1.0000000000f, -0.1913417162f, +0.0947343455f, -0.5924659585f, 
+0.0000000000f, +0.5924659585f, -0.0947343455f, +0.1913417162f, +1.0000000000f, +0.4174197128f, +0.3535533906f, +1.2552931065f, 
+0.8660254038f, +0.4619397663f, +1.3194792169f, +1.1827865776f, +0.5000000000f, +1.1827865776f, +1.3194792169f, +0.4619397663f, 
+0.8660254038f, +1.2552931065f, +0.3535533906f, +0.4174197128f, +1.0000000000f, +0.1913417162f, -0.0947343455f, +0.5924659585f, 
+0.0000000000f, -0.5924659585f, +0.0947343455f, -0.1913417162f, -1.0000000000f, -0.4174197128f, -0.3535533906f, -1.2552931065f, 
-0.8660254038f, -0.4619397663f, -1.3194792169f, -1.1827865776f, -0.5000000000f, -1.1827865776f, -1.3194792169f, -0.4619397663f, 
-0.8660254038f, -1.2552931065f, -0.3535533906f, -0.4174197128f, -1.0000000000f, -0.1913417162f, +0.0947343455f, -0.5924659585f, 
+0.0000000000f, +0.5924659585f, -0.0947343455f, +0.1913417162f, +1.0000000000f, +0.4174197128f, +0.3535533906f, +1.2552931065f, 
+0.8660254038f, +0.4619397663f, +1.3194792169f, +1.1827865776f, +0.5000000000f, +1.1827865776f, +1.3194792169f, +0.4619397663f, 
+0.8660254038f, +1.2552931065f, +0.3535533906f, +0.4174197128f, +1.0000000000f, +0.1913417162f, -0.0947343455f, +0.5924659585f, 
+0.0000000000f, -0.5924659585f, +0.0947343455f, -0.1913417162f, -1.0000000000f, -0.4174197128f, -0.3535533906f, -1.2552931065f, 
-0.8660254038f, -0.4619397663f, -1.3194792169f, -1.1827865776f, -0.5000000000f, -1.1827865776f, -1.3194792169f, -0.4619397663f, 
-0.8660254038f, -1.2552931065f, -0.3535533906f, -0.4174197128f, -1.0000000000f, -0.1913417162f, +0.0947343455f, -0.5924659585f, 
-0.0000000000f, +0.5924659585f, -0.0947343455f, +0.1913417162f, +1.0000000000f, +0.4174197128f, +0.3535533906f, +1.2552931065f, 
+0.8660254038f, +0.4619397663f, +1.3194792169f, +1.1827865776f, +0.5000000000f, +1.1827865776f, +1.3194792169f, +0.4619397663f, 
+0.8660254038f, +1.2552931065f, +0.3535533906f, +0.4174197128f, +1.0000000000f, +0.1913417162f, -0.0947343455f, +0.5924659585f, 
-0.0000000000f, -0.5924659585f, +0.0947343455f, -0.1913417162f, -1.0000000000f, -0.4174197128f, -0.3535533906f, -1.2552931065f, 
-0.8660254038f, -0.4619397663f, -1.3194792169f, -1.1827865776f, -0.5000000000f, -1.1827865776f, -1.3194792169f, -0.4619397663f, 
-0.8660254038f, -1.2552931065f, -0.3535533906f, -0.4174197128f, -1.0000000000f, -0.1913417162f, +0.0947343455f, -0.5924659585f, 
+0.0000000000f, +0.5924659585f, -0.0947343455f, +0.1913417162f, +1.0000000000f, +0.4174197128f, +0.3535533906f, +1.2552931065f, 
+0.8660254038f, +0.4619397663f, +1.3194792169f, +1.1827865776f, +0.5000000000f, +1.1827865776f, +1.3194792169f, +0.4619397663f, 
+0.8660254038f, +1.2552931065f, +0.3535533906f, +0.4174197128f, +1.0000000000f, +0.1913417162f, -0.0947343455f, +0.5924659585f, 
+0.0000000000f, -0.5924659585f, +0.0947343455f, -0.1913417162f, -1.0000000000f, -0.4174197128f, -0.3535533906f, -1.2552931065f, 
-0.8660254038f, -0.4619397663f, -1.3194792169f, -1.1827865776f, -0.5000000000f, -1.1827865776f, -1.3194792169f, -0.4619397663f, 
-0.8660254038f, -1.2552931065f, -0.3535533906f, -0.4174197128f, -1.0000000000f, -0.1913417162f, +0.0947343455f, -0.5924659585f, 
-0.0000000000f, +0.5924659585f, -0.0947343455f, +0.1913417162f, +1.0000000000f, +0.4174197128f, +0.3535533906f, +1.2552931065f, 
+0.8660254038f, +0.4619397663f, +1.3194792169f, +1.1827865776f, +0.5000000000f, +1.1827865776f, +1.3194792169f, +0.4619397663f, 
+0.8660254038f, +1.2552931065f, +0.3535533906f, +0.4174197128f, +1.0000000000f, +0.1913417162f, -0.0947343455f, +0.5924659585f, 
+0.0000000000f, -0.5924659585f, +0.0947343455f, -0.1913417162f, -1.0000000000f, -0.4174197128f, -0.3535533906f, -1.2552931065f, 
-0.8660254038f, -0.4619397663f, -1.3194792169f, -1.1827865776f, -0.5000000000f, -1.1827865776f, -1.3194792169f, -0.4619397663f, 
-0.8660254038f, -1.2552931065f, -0.3535533906f, -0.4174197128f, -1.0000000000f, -0.1913417162f, +0.0947343455f, -0.5924659585f, 
-0.0000000000f, +0.5924659585f, -0.0947343455f, +0.1913417162f, +1.0000000000f, +0.4174197128f, +0.3535533906f, +1.2552931065f, 
+0.8660254038f, +0.4619397663f, +1.3194792169f, +1.1827865776f, +0.5000000000f, +1.1827865776f, +1.3194792169f, +0.4619397663f, 
+0.8660254038f, +1.2552931065f, +0.3535533906f, +0.4174197128f, +1.0000000000f, +0.1913417162f, -0.0947343455f, +0.5924659585f, 
+0.0000000000f, -0.5924659585f, +0.0947343455f, -0.1913417162f, -1.0000000000f, -0.4174197128f, -0.3535533906f, -1.2552931065f, 
};


```

```c
#include "stm32f4xx_hal.h"              // Keil::Device:STM32Cube HAL:Common
#include "arm_math.h"                   // ARM::CMSIS:DSP

#define SIG_LENGTH 320

uint32_t freq;

extern void SystemClock_Config(void);
extern float32_t inputSignal_f32_1kHz_15kHz[SIG_LENGTH];
void plot_Sin_wave(void);

float32_t inputSample;

int main (void) {
    
    HAL_Init();
    SystemClock_Config();
    freq = HAL_RCC_GetHCLKFreq();
    
    plot_Sin_wave();
    
    while(1) {
    }
}

void plot_Sin_wave(void) {
    int i, j;
    
      for(i = 0; i < SIG_LENGTH; i++) {
            
            inputSample = inputSignal_f32_1kHz_15kHz[i];
            for(j = 0; j < 3000; j++) {}; // delay
                
        }
}

void SysTick_Handler(void) {
    HAL_IncTick();
    HAL_SYSTICK_IRQHandler();
}


```

![](https://i.imgur.com/dc73zPX.png)



## 3. Filter by given coefficient

Given coeff below

```c
const float32_t firCoeffs32[NUM_TAPS] = {
  -0.0018225230f, -0.0015879294f, +0.0000000000f, +0.0036977508f, +0.0080754303f, +0.0085302217f, -0.0000000000f, -0.0173976984f,
  -0.0341458607f, -0.0333591565f, +0.0000000000f, +0.0676308395f, +0.1522061835f, +0.2229246956f, +0.2504960933f, +0.2229246956f,
  +0.1522061835f, +0.0676308395f, +0.0000000000f, -0.0333591565f, -0.0341458607f, -0.0173976984f, -0.0000000000f, +0.0085302217f,
  +0.0080754303f, +0.0036977508f, +0.0000000000f, -0.0015879294f, -0.0018225230f
};
```

### Source code

```c
#include "stm32f4xx_hal.h"              // Keil::Device:STM32Cube HAL:Common
#include "arm_math.h"                   // ARM::CMSIS:DSP

#define SIG_LENGTH      320
#define NUM_TAPS        29
#define BLOCK_SIZE      32

uint32_t freq;

extern void SystemClock_Config(void);
extern float32_t inputSignal_f32_1kHz_15kHz[SIG_LENGTH];
void plot_Sin_wave(void);
void plot_output_signal(void);
void plot_both(void);

// Variable for logic analyzer
float32_t inputSample;
float32_t outputSample;


/// FIR Low-pass filter
/// Given Coeff
const float32_t firCoeffs32[NUM_TAPS] = {
    -0.0018225230f, -0.0015879294f, +0.0000000000f, +0.0036977508f, +0.0080754303f, +0.0085302217f, -0.0000000000f, -0.0173976984f,
    -0.0341458607f, -0.0333591565f, +0.0000000000f, +0.0676308395f, +0.1522061835f, +0.2229246956f, +0.2504960933f, +0.2229246956f,
    +0.1522061835f, +0.0676308395f, +0.0000000000f, -0.0333591565f, -0.0341458607f, -0.0173976984f, -0.0000000000f, +0.0085302217f,
    +0.0080754303f, +0.0036977508f, +0.0000000000f, -0.0015879294f, -0.0018225230f
};

static float32_t firState_f32[BLOCK_SIZE + NUM_TAPS - 1];

const uint32_t NUM_OF_BLOCKs = SIG_LENGTH / BLOCK_SIZE;

float32_t outputSignal_f32[SIG_LENGTH];



int main (void) {

    HAL_Init();
    SystemClock_Config();
    freq = HAL_RCC_GetHCLKFreq();



/// FIR Low-pass filter
// ARM DSP API
// 1. Create fir object
    arm_fir_instance_f32 _1Khz_Khz_sig;
// 2. Initialize
    arm_fir_init_f32(
                &_1Khz_Khz_sig,               // points to an instance of the floating-point FIR filter structure
                NUM_TAPS,                     // number of filter coefficients in the filter
                (float32_t *)&firCoeffs32[0], // points to the filter coefficients buffer
                &firState_f32[0],             // points to the state buffer
                BLOCK_SIZE                    // number of samples processed per call
                );
// 3. Calculate 
    for(int i = 0; i < NUM_OF_BLOCKs; i++) {
        arm_fir_f32(
            &_1Khz_Khz_sig, 
                &inputSignal_f32_1kHz_15kHz[0] + (i * BLOCK_SIZE), // filter out block by block
                &outputSignal_f32[0] + (i * BLOCK_SIZE),           // store output block by block
                BLOCK_SIZE
                );
    }


// plot_Sin_wave();
// plot_output_signal();
    plot_both();

    while(1) {};
}

void plot_Sin_wave(void) {
    int i = 0, j;

    while(1) {

        inputSample = inputSignal_f32_1kHz_15kHz[i];
for(j = 0; j < 3000; j++) {}; // delay

    i = (i == SIG_LENGTH - 1) ? 0 : i + 1;
}
}


void plot_output_signal (void) {
    int i = 0, j;

    for(i = 0; i < SIG_LENGTH; i++) {
        outputSample = outputSignal_f32[i];

        for(j = 0; j < 3000; j++) {}

    }
}

void plot_both(void) {
    int i, j;

    for(i = 0; i < SIG_LENGTH; i++) {
        inputSample = inputSignal_f32_1kHz_15kHz[i];
        outputSample = outputSignal_f32[i];

        for(j = 0; j < 8000; j++) {}
//if(i == SIG_LENGTH - 1) i = 0;
    }
}

void SysTick_Handler(void) {
    HAL_IncTick();
    HAL_SYSTICK_IRQHandler();
}
```

![](https://i.imgur.com/C653v8C.png)

## Signal Generation via Octive

* Generate 10 Hz sin wave for 3 sec
* Save as txt file

```m
f = 10; # 10Hz
Amp = 1;
ts = 1/100;
T = 3;
t = 0:ts:T;
y = Amp*sin(2*pi*f*t);

csvwrite('_10hz_signal.txt', y);
```




## Reference

* http://www.keil.com/pack/doc/cmsis/DSP/html/group__FIR.html



