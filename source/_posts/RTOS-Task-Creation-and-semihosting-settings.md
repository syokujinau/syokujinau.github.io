---
title: FreeRTOS Task Creation 與 Semi-hosting (USART) 設定
date: 2020-02-07 20:25:38
tags:
- Embedded
- Operating System
categories:
- [Embedded, FreeRTOS]
thumbnail: https://i.imgur.com/I3hWsho.png
---

<img src="https://i.imgur.com/XdMD09Y.jpg" width="40%">
 
通常一個應用可以將個功能切分為多個task，各個task的資料相互傳遞、存取皆可透過FreeRTOS的kernel services完成，本篇紀錄基本的task creation API並設定semi-hosting在eclipse開發環境印出uart傳遞至PC的資料。

![](https://i.imgur.com/4TwZB3B.png)

每次產生task，FreeRTOS會做以下動作

![](https://i.imgur.com/lNcoiyz.png)

> RTOS的Task可視為作業系統課本提到的process

在context switch時，usStackDepth為stack空間來儲存local variables以及context of task 

```c
BaseType_t xTaskCreate(    
    TaskFunction_t pvTaskCode,  
    const char * const pcName,
    configSTACK_DEPTH_TYPE usStackDepth, // 給定該task的stack空間,單位是words
    void *pvParameters,
    UBaseType_t uxPriority,
    TaskHandle_t *pxCreatedTask // task handler指向TCB位址
);
```

<!-- more -->

<!-- 
* task handler指向TCB
![](https://i.imgur.com/CsHm6Bk.png) -->

<!-- * What happens when a TASK be created?
    * [![](https://i.imgur.com/sP7QPk4.png)
](https://www.udemy.com/course/mastering-rtos-hands-on-with-freertos-arduino-and-stm32fx/learn/lecture/6383708#questions/7078890)
 -->
 
task配置在ram記憶體中的heap section
 
 ![](https://i.imgur.com/LA20ceU.png)


單一task在round robin scheduler的執行模式

<!-- ![](https://i.imgur.com/cr52kkR.png)
 -->

![](https://i.imgur.com/4u2UEQe.png)



## 設定semi-host

<!-- > [完整流程](https://www.udemy.com/course/mastering-rtos-hands-on-with-freertos-arduino-and-stm32fx/learn/lecture/12831652#questions/8121341) -->

STM32 debugger提供semihosting功能，下圖target是arm開發板，只有在debug mode可以使用，讓host的電腦幫我們做printf等指令。

<!-- ![](https://i.imgur.com/evZw4bF.png) -->
![](https://i.imgur.com/Gpyp9OX.png)



照以下步驟設定

1. GCC Linker加入參數`-specs=rdimon.specs -lc -lrdimon`
![](https://i.imgur.com/TSkIo5v.png)
按apply > OK
2. Debug configuration of your application `monitor arm semihosting enable`
![](https://i.imgur.com/uGnAmtA.png)
3. In main.c use 
```c
extern void initialise_monitor_handles();
initialise_monitor_handles();
```
4. Check `Exclude` for syscalls.c
![](https://i.imgur.com/wbZc4QL.png)

### USE SEMIHOSTING Preprocessor

在有使用semi-hosting的程式碼加上directives(指示詞)，並按照下圖設置，如果defined symbols的列表中有`USE_SEMIHOSTING`的話編譯前preporcessor就會包含這個#ifdef的區塊。

```c 
#ifdef USE_SEMIHOSTING
// used for semihosting
extern void initialise_monitor_handles();
#endif
```

![](https://i.imgur.com/91e1vBy.png)



## UART Communication with PC

### UART GPIO Configuration

```c
static void prevSetupUart(void) {
	// peripheral related initialization

	// 1. Enable the UART2 and GPIOA Peripheral clock
	RCC_APB1PeriphClockCmd(RCC_APB1Periph_USART2, ENABLE);
	RCC_AHB1PeriphClockCmd(RCC_AHB1Periph_GPIOA, ENABLE);

	// 2.
	// PA2: UART2_TX
	// PA3: UART2_RX
	// Alternate function configuration of MCU pins to behave as UART2 Tx, Rx
	GPIO_InitTypeDef gpio_uart_pins;

	memset(&gpio_uart_pins, 0, sizeof(gpio_uart_pins)); // clear the structure
	gpio_uart_pins.GPIO_Pin = GPIO_Pin_2 | GPIO_Pin_3;
	gpio_uart_pins.GPIO_Mode = GPIO_Mode_AF;
	gpio_uart_pins.GPIO_PuPd = GPIO_PuPd_UP; // for idle state

	GPIO_Init(GPIOA, &gpio_uart_pins);

	// 3. AF mode settings for the pins
	GPIO_PinAFConfig(
			GPIOA,            // PA2
			GPIO_PinSource2,  // PA2
			GPIO_AF_USART2
	);
	GPIO_PinAFConfig(
			GPIOA,            // PA3
			GPIO_PinSource3,  // PA3
			GPIO_AF_USART2
	);

	// 4. UART parameter initializations
	USART_InitTypeDef uart2_init;

	memset(&uart2_init, 0, sizeof(uart2_init)); // clear the structure
	uart2_init.USART_BaudRate = 115200;
	uart2_init.USART_HardwareFlowControl = USART_HardwareFlowControl_None;
	uart2_init.USART_Mode = USART_Mode_Tx | USART_Mode_Rx;
	uart2_init.USART_Parity = USART_Parity_No;
	uart2_init.USART_StopBits = USART_StopBits_1;
	uart2_init.USART_WordLength = USART_WordLength_8b;

	USART_Init(
			USART2,
			&uart2_init
	);

}
```

> 因為IDLE state在UART是高電位(logic 1)
> ![](https://i.imgur.com/yz94KmL.png)
> 所以 `gpio_uart_pins.GPIO_PuPd`需設成`GPIO_PuPd_UP;`

### Create Tasks

```c
void vTask1_handler(void *params) {

	while(1){
		printMsg("Hello, I'm task-1\r\n");
	}

}


void vTask2_handler(void *params) {

	while(1){
		printMsg("Hello, I'm task-1\r\n");
	}

}
```

Result：
![](https://i.imgur.com/a3MelSd.png)



<!-- The strings sent from task-1 and task-2 show alternately, because they are same priority, the tasks actually preempted each other in the middle of transmissiom.

**There two tasks which used the same UART data register (resource), therefore, the synchronization like usages of semaphores, mutexes, etc.** -->

交互顯示task-1與task-2的字串，但因為他們的priority一樣，且共用UART data register，所以他們會在資料傳輸中互相preempt，可以用semaphores, mutexes等來保護critical section，以下用簡單的key方法解決。

#### UART_ACCESS_KEY and taskYIELD()

<!-- * `UART_ACCESS_KEY` stands for a key, **if the key is available**, the task is able to access critial section.
* `taskYIELD()` api of FreeRTOS can **manually trigger the context switch** and **force task to leave CPU**, otherwise task-2 may not have the chance to get the key. -->

`UART_ACCESS_KEY` 代表鑰匙，如果鑰匙可取得, 則此task可存取critial section，`taskYIELD()` api 可以主動觸發context switch並**強制task離開CPU**, 否則task-2沒有機會取得鑰匙。


```c
void vTask1_handler(void *params) {

    while(1){

        if(UART_ACCESS_KEY == AVAILABLE) {
        
            UART_ACCESS_KEY = NON_AVAILABLE;
            printMsg("Hello, I'm task-1\r\n");
            UART_ACCESS_KEY = AVAILABLE;
            taskYIELD();
            
        }

    }

}


void vTask2_handler(void *params) {

    while(1){

        if(UART_ACCESS_KEY == AVAILABLE) {

            UART_ACCESS_KEY = NON_AVAILABLE;
            printMsg("Hello, I'm task-2\r\n");
            UART_ACCESS_KEY = AVAILABLE;
            taskYIELD();

        }

    }

}
```



Result：
![](https://i.imgur.com/qAy6N2N.png)


> source code: https://github.com/syokujinau/ARM_RTOS_Projects/blob/master/RTOS_Workspace/STM32_UART/src/main.c


