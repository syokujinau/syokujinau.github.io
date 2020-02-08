---
title: FreeRTOS API Interface, Synchronization & Coding Style
date: 2020-01-08 11:32:58
tags:
- Embedded
- Operating System
categories:
- [Embedded, FreeRTOS]
thumbnail: https://i.imgur.com/xj14dYO.png
---



## API Interface

這張圖是FreeRTOS與硬體的關係，提供Semaphore, Queue, Timer, Task 等kernel services，來讓我們架構上層的應用，該應用可以包含n個task，透過kernel與device driver ([Board support package Layer](https://www.freertos.org/FreeRTOS-Plus/BSP_Solutions/FreeRTOS_BSP.html))溝通，執行各種操作，例如UART driver, SPI driver等，在由driver控制硬體週邊（SPI, I2C, Timer, GPIOs, etc），FreeRTOS完全不包含任何跟硬體周邊有關的程式。

<!-- ![](https://i.imgur.com/Ogsi4gZ.png) -->
![](https://i.imgur.com/Bos9Ird.png)


<!-- * Create Task, Semaphore, Queue... , we actually consume the system heap.
    * ![](https://i.imgur.com/6NXRxpo.png) -->

用FreeRTOS API產生Task, Semaphore, Queue...等, 會消耗system heap的記憶體

![](https://i.imgur.com/R1ldlqY.png)

<!-- more -->

<!-- * FreeRTOS have 5 Heap management schemes, the first one only can malloc but not free heap memory.
    * ![](https://i.imgur.com/CrxEMtT.png)
 -->

FreeRTOS提供五種heap management schemes，第一種只能malloc，不能free heap memory，也可以寫自己定義的scheme

![](https://i.imgur.com/pIRwVLB.png)


## RTOS同步(Synchronization)

RTOS需要能處理同步問題，用Producer & Consumer描述task的行為

![](https://i.imgur.com/X8obreX.png)

<!-- 
![](https://i.imgur.com/v3Maf0E.png) -->

可以做到signaling的方法有下列幾種

1. Events (Flags)
2. Semaphores (Counting & Binary)
3. Mutex
4. Queue and Message Queue
5. Pips
6. Mailboxes
7. Signals (UNIX like signals)

<!-- ![](https://i.imgur.com/5SMNwxs.png) -->



## FreeRTOS Coding Style

### Macros

巨集的前綴可以知道有在哪個檔案中被定義，通常前綴是小寫
例如：FreeRTOSConfig.h中定義`configUSE_PREEMPTION`
<!-- 
![](https://i.imgur.com/QIuY0Ss.png)
 -->

### Variables

變數的命名慣例：



| type | prefix | 
| -------- | -------- |
| unsigned long     | `ul`     | 
| unsigned short | `us` |
| unsigned char|`uc` |
| non stdint | `x` |
| unsigned non stdint | `u`|
| enumerate| `e` |

指標則會再加個`p`，例如uint16_t的指標是`pus`



<!-- ![](https://i.imgur.com/X9UEJEE.png)
 -->
 
<!--  
![](https://i.imgur.com/FVMPI9N.png) -->



### Function

函式的命名慣例是前綴代表回傳值的類型，而`v`代表void
例如：task.c內定義的vTaskDelete()，回傳void

此外file scope static function (private function)的前綴是`prv`
<!-- 
![](https://i.imgur.com/YGUoqns.png) -->

