---
title: FreeRTOS - Hook Function
date: 2019-12-09 12:32:58
tags:
- Embedded
- Operating System
categories:
- [Embedded, FreeRTOS]
thumbnail: https://i.imgur.com/rjbUjJo.png
---

> 參考：https://www.freertos.org/a00016.html


* Idle task hook function
* RTOS Tick hook function
* Dynamic memory allocation (Malloc) faild hook function 
* Stack overflow hook function

必要時實做這些hook function，當有該事件發生rtos就會自動執行他們


<!-- ![](https://i.imgur.com/1zP1mS3.png)
 -->
## Idle Task Hook Function

idle task作為閒置時rtos的要做的工作，要在**FreeRTOSConfig.h**中設定`configUSE_IDLE_HOOK`為1，並實做`void vApplicationIdleHook(void);`，一般可以在閒置中降低mcu運算量節能功耗
<!-- 
![](https://i.imgur.com/q9N4Icz.png) -->

<!-- more -->

```c
void vApplicationIdleHook(void) {
    // configUSE_IDLE_HOOK should be 1 in FreeRTOSConfig.h
    // idle task implementation 
}
```

## RTOS Tick Hook Function

```c
void vApplicationTickHook(void) {
    // configUSE_TICK_HOOK should be 1 in FreeRTOSConfig.h
    // tick task implementation 
}
```

## Malloc Failed Hook Function

```c
void vApplicationMallocFailedHook(void) {
    // configUSE_MALLOC_FAILED_HOOK should be 1 in FreeRTOSConfig.h
    // malloc failed task implementation 
}
```

## Stack Overflow Hook Function

```c
void vApplicationStackOverflowHook(TaskHandle_t xTask, signed char *pcTaskName) {
    // configUSE_FOR_STACK_OVERFLOW should be 1 in FreeRTOSConfig.h
    // stack overflow task implementation 
}
```

<!-- ![](https://i.imgur.com/lBOFnvt.png) -->



<!-- ![](https://i.imgur.com/qoee8Sl.png) -->




<!-- ![](https://i.imgur.com/I0XIfM4.png) -->



<!-- ![](https://i.imgur.com/W35Bupi.png) -->

<!-- * see project5 on Github
    * `FreeRTOSConfig.h`
        * `#define configUSE_IDLE_HOOK 1`
        * implement `vApplicationIdleHook()` -->

<!-- ![](https://i.imgur.com/3EPePCg.png)


![](https://i.imgur.com/KZlbLpj.png) -->



## Example 

要讓mcu進入sleep mode，可用WFI(Wait for interrupt)這個16-bit Thumb instruction，適用於Cortex-M0, M3, M4與M7系列，當執行`__WFI()`，processor會暫停clock，停止指令執行，直到下個中斷發生或者進入debug模式，用於有條件的進入sleep狀態，有分normal與deep sleep mode。

> 使用硬體: [NUCLEO-F411RE](https://www.st.com/en/evaluation-tools/nucleo-f411re.html)
> <img src="https://i.imgur.com/dSYfyEi.png" width="60%">
> data brief: https://www.st.com/resource/en/data_brief/nucleo-f411re.pdf

<script src="https://gist.github.com/syokujinau/2476d7f76a9f27769b8f83266a64e717.js"></script>