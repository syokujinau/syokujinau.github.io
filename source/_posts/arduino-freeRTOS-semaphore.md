---
title: Arduino與FreeRTOS - 簡介與Multual exclusion 解決方法
date: 2019-09-10 15:48:30
tags:
- Embedded 
- Semaphore 
- Multual exclusion
- Operating System
categories:
- [Embedded, FreeRTOS]
thumbnail: https://i.imgur.com/4UVLZn1.png
---

![](https://i.imgur.com/8rVf5V5.png)

## RTOS 

一般談到OS常見像是Windows、macOS、Linux等等，稱為General Purpose OS，RTOS全名是Real-time Operating System，實時作業系統，FreeRTOS是個開源的RTOS作業系統，主要支援ARM系列微控制器，各個MCU vendor皆有對應的版本(主要在`port.c`定義)。

RTOS必須對每個執行的critical operation有一個已知的maximum time，並能做到
* 處理 interrupts 與 internal system exceptions
* 維護 Critical Sections
* 執行合適的 Scheduling Mechanism

RTOS中事件觸發到能夠進入CPU的時間(**Task switching latency**)是定值，所以能夠在有限時間執行重要工作，並且IRQ觸發生到進入ISR的時間極短(**Interrupt latency**)，例如汽車中的安全氣囊，車禍發生，感應器偵測到衝擊(IRQ occurs)瞬間到使氣囊充氣(ISR handles)必須非常迅速才能有效保護車內人員。個人認為real-time涵義上，主要表達Task的執行是**predictable**，而非執行速度快(這應是由 clock 決定)。

<!-- more -->

## Download

在Arduino下載並使用FreeRTOS非常容易，在Library Manager搜尋安裝即可，並提供一些範例可以直接執行。

![](https://i.imgur.com/JmbDzxh.png)


## Usage

一般Arduino的架構會用所謂polling做所有的工作，一個`setup`做硬體資源設定(啟用UART、設定GPIO等等)，並用一個`loop`執行一堆東西，基本上就是一個single thread的程式，Arduino常用的delay(1000)就是一個浪費CPU資源的指令，因為你的Arduino這時候只是發呆個1秒左右，才做其他指令，使用multi-thread好處是提高cpu utilization，我們可以create多個task，並在task handler做各種不同的工作。



### Task 

> Arduino FreeRTOS Github - [tasks.c](https://github.com/feilipu/Arduino_FreeRTOS_Library/blob/master/src/tasks.c)

可以視為Thread，RTOS預設採用priority based pre-emptive scheduling(可在`FreeRTOSConfig.h`裡面修改設定)來對多個task做排程，在一個task產生時，RTOS會做以下幾件事

1. 在RAM的system heap產生task control block並初始化
2. 在system heap產生這個task專用的stack (所以需要宣告stack deep)
    * FreeRTOS的kernel service (Task、Queue、Semaphore, etc)都要消耗Arduino的heap記憶體空間
4. 放進ready list 等待排程 
    * task status參考: https://www.freertos.org/RTOS-task-states.html

來看看create一個task需要哪些參數。

```c 
BaseType_t xTaskCreate( 
    TaskFunction_t pxTaskCode,                  // task handler 
    const char * const pcName,                  // task 名稱
    const configSTACK_DEPTH_TYPE usStackDepth,  // task所需stack size
    void * const pvParameters, 
    UBaseType_t uxPriority,                     // task的優先權
    TaskHandle_t * const pxCreatedTask          // placeholder
);
```

做個實驗來讓2個優先權相同的task透過UART傳送資料到電腦

```cpp
#include <Arduino_FreeRTOS.h>

// prototypes
void Task1(void *params) ;
void Task2(void *params) ;
 
void setup() {
 
  Serial.begin(9600);
 
  xTaskCreate(
    Task1,
    (const portCHAR *) "Task 1",
    128,  // Stack size  
    NULL,  
    1,  // Priority
    NULL 
  );

  xTaskCreate(
    Task2,
    (const portCHAR *) "Task 2",
    128,  // Stack size  
    NULL,  
    1,  // Priority
    NULL 
  );

  
}

void loop() {}




void Task1(void *params) {

  while(1) {
    Serial.println("Task 1");
  }
  
}

void Task2(void *params) {

  while(1) {
    Serial.println("Task 2");
  }
  
}
```

輸出結果將會是一團糟

![](https://i.imgur.com/zvs8foR.png)

這是因為 UART 的 data register 是 Critical section ，也就是具有multual exclusion性質的記憶體資源，好比你家廁所，一次只能有一個人使用，應該不會有人想跟人共用廁所吧= =|||，於是我們可以加裝門鎖，廁所有人用的時候鎖住，就可以避免這種問題。一般具有multual exclusion性質的資源像是global variable、MCU周邊(GPIO、UART、SPI、I2C等等)，這時候mutex、semaphore等等kernel service就派上用場。

```cpp
#include <Arduino_FreeRTOS.h>
#include <semphr.h> 

// prototypes
void Task1(void *params) ;
void Task2(void *params) ;

SemaphoreHandle_t xSerialSemaphore;


void setup() {
 
  Serial.begin(9600);

  vSemaphoreCreateBinary(xSerialSemaphore);

  if(xSerialSemaphore != NULL) { // 如上面所說，semaphore會占用system heap，
                                 // 所以要確認他有沒有成功allocated

    xSemaphoreGive(xSerialSemaphore); // 一開始給出鑰匙
 
    xTaskCreate(
      Task1,
      (const portCHAR *) "Task 1",
      128,  // Stack size  
      NULL,  
      1,  // Priority
      NULL 
    );
  
    xTaskCreate(
      Task2,
      (const portCHAR *) "Task 2",
      128,  // Stack size  
      NULL,  
      1,  // Priority
      NULL 
    );
    
  }
  else {
    
    Serial.println("Semaphore creation failed...");  
    
  }
  
}

void loop() {}




void Task1(void *params) {

  while(1) {
    xSemaphoreTake(xSerialSemaphore, portMAX_DELAY );
    Serial.println("Task 1");
    xSemaphoreGive(xSerialSemaphore);
    vTaskDelay(pdMS_TO_TICKS(10)); // 強制進入block status以免該task又馬上取得鑰匙
  }
  
}

void Task2(void *params) {

  while(1) {
    xSemaphoreTake(xSerialSemaphore, portMAX_DELAY );
    Serial.println("Task 2");
    xSemaphoreGive(xSerialSemaphore);
    vTaskDelay(pdMS_TO_TICKS(10));
  }
  
}
```

> `vTaskDelay(1000)` 是daley 1000個ticks，可以用 `pdMS_TO_TICKS(1000)` 換算1000 ms所需的tick數

![](https://i.imgur.com/26pWJKF.png)




### Binary Semaphore v.s. Mutex

常搞混的mutex與binary semaphore，上面的應用的確也能使用mutex來保護critical section，先簡單說，binary semaphore還是可能出現priority inversion的情況，這是RTOS應該避免的，mutex有實作priority inheritance機制(缺點是要比較多記憶體maintain mutex)，繼承higher priority來最小化priority inversion的發生，有空在寫一篇詳細討論吧(會這樣講就是沒空...)
