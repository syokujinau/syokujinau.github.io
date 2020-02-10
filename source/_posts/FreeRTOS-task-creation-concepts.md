---
title: FreeRTOS - Task Creation觀念彙整
date: 2019-12-13 11:01:28
tags:
- Embedded
- Operating System
categories:
- [Embedded, FreeRTOS]
thumbnail: https://i.imgur.com/qLhg02D.png
---

<img src="https://i.imgur.com/kIXupfv.png" width="45%" />


1. 建立一個stack為512 bytes的task需要佔用多少記憶體空間？
	* 512 + sizeof(TCB) bytes
	* 同時Task Control Block也在stack中動態配置。TCB結構可在FreeRTOS kernel原始碼的[task.c](https://github.com/FreeRTOS/FreeRTOS-Kernel/blob/82e80521124c6e96e5fdb3538a2533f994e2db8f/tasks.c#L252)中找到。
2. TCB結構的第一個成員是？
	* [ ]  task state
	* [ ]  task size
	* [ ]  task priority
	* [x]  指向task stack頂端的指標
<!-- more -->
3. 直到啟動scheduler以前task都不會執行？
	* true
	* 透過`vTaskStartScheduler()`啟動
4. FreeRTOS的預設scheduler是基於哪種scheduling algorithm？
	* priority based preemptive scheduling
4. 在priority based preemptive scheduling下，有高優先權與低優先權的2個task，目前CPU由高優先權task佔用，如何使低優先權task能夠執行？
	* 對高優先權task的blocking, suspending 或 task yielding 可使低優先權task得到CPU使用權
5. 是否可以靜態配置task與靜態配置的優點？
	* 可
	* 優點：RTOS物件可以置於特定位址(RAM的全域空間)、最大ram使用量可在link time被決定而不是run time、開發者不需考慮動態配置失敗的問題、讓FreeRTOS可使用在禁止動態配置記憶體的應用
	* https://www.freertos.org/xTaskCreateStatic.html
	* https://www.freertos.org/Static_Vs_Dynamic_Memory_Allocation.html
6. 宣告並初始化在task函式中的靜態變數實際上佔用哪個記憶體空間？ ![](https://i.imgur.com/ssVObuI.png)
	* [ ] stack of task
	* [ ] stack of RAM
	* [ ] heap of RAM 
	* [x] global space of RAM
7. 宣告並初始化在task函式中的非靜態變數實際上佔用哪個記憶體空間？ ![](https://i.imgur.com/zNuL8pU.png)
	* [x] stack of task
	* [ ] stack of RAM
	* [ ] heap of RAM 
	* [ ] global space of RAM