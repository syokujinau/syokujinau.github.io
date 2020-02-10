---
title: FreeRTOS - Scheduler與設定檔 觀念彙整
date: 2019-12-13 12:01:28
tags:
- Embedded
- Operating System
categories:
- [Embedded, FreeRTOS]
thumbnail: https://i.imgur.com/Jimal0z.png
---

<img src="https://i.imgur.com/kIXupfv.png" width="45%" />


1. 如何使用co-operative排程而不是預設的preemptive？
	* FreeRTOSConfig.h 中將`configUSE_PREEMPTION`定義為0
2. 定義time slice
	* 兩個kernal tick (SysTick)中斷的時間差
2. 如何調整time slice？
	* 修改`configTICK_RATE_HZ`的值
4. FreeRTOSConfigh.h 與MCU硬體無關？
	* False
	* 其中定義的中斷優先權會根據硬體而有所差異
<!-- more -->
5. 若`configTICK_RATE_HZ`設為125 Hz，則time slice為？
	* $\frac{1}{125} = 8$ ms
6. `configTICK_RATE_HZ`設為125 Hz，則32 ms的時間間隔將產生多少tick interrupt？
	* 4
7. SysTick Timer是否可被用在其他用途？
	* False
	* SysTick Timer只能用於kernel ticking
8. Kernel ticking是否能用其他MCU的Timer週邊取代SysTick Timer？
	* Yes
9. FreeRTOS中是誰觸發context switching？
	* SysTick inturrput handler
10. ARM Cortex-M處理器中誰負責執行context switching？	
	* PendSV inturrput handler
11. SVC exception會在FreeRTOS應用中觸發幾次？
	* SVC被用於啟動scheduler所以只有1次
12. ARM Cortex-M處理器的Task yielding與哪個exception有關？
	* PendSV
13. PendSV exception的預設優先權為多少？(假設NVIC_PRIO_BITS = 4)
	* ![](https://i.imgur.com/mJW7keU.png)代表有16個優先權 
	* <img src="https://i.imgur.com/EjWC25q.png" width="45%">
	* PendSV exception的優先權最大(值最小)，NVIC_PRIO_BITS = 4的情況下優先權最大為0xF0
14. 在cotext switching時有哪些register會被存到stack中？
	* R0, R1, R2, R3, R12, LR, PC, xPSR
	* ![](https://i.imgur.com/eelQzC1.png)
	* 其中PSP為private stack pointer
15. 哪個FreeRTOS函數會設置SVC,PendSV, SysTick exceptions？
	* vPortStartScheduler()
16. SysTick timer是ARM Cortex-M處理器外的週邊
	* False
	* SysTick是ARM Cortex-M處理器內部的timer engine
17. MCU System Clock為16 MHz且`configTICK_RATE_HZ`為1000 Hz的情況下，SysTick counter的值為？
	* SYSTICK_LOAD_REG = ( configSYSTICK_CLOCK_HZ / configTICK_RATE_HZ ) - 1 = 16000 - 1 = 15999