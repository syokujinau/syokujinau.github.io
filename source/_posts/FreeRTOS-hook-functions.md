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


```c
#include "stm32f4xx.h"
#include "FreeRTOS.h"
#include "task.h"
#include <string.h>
#include <stdint.h>
#include <stdio.h>
			

// task handle for task notification
TaskHandle_t xTaskHandle1 = NULL;
TaskHandle_t xTaskHandle2 = NULL;

// prototypes
static void prevSetupUart(void);
static void preSetupGPIO(void);
static void prvSetupHardware(void);
void printMsg(char *msg);

// UART
char user_msg[250] = {0};
// task handler prototypes
void vTask1_handler(void *params);
void vTask2_handler(void *params);

// Global variable for flags
#define FALSE        0
#define TRUE         1
#define NOT_PRESSED  FALSE
#define PRESSED      TRUE
uint8_t button_status_flag = NOT_PRESSED;

int main(void)
{
	// SEGGER
	DWT->CTRL |= (1 << 0);
	// 1. Reset the RCC clock config to the default reset state.
	// HSI On, PLL Off, system clock = cpu clock = 16MHz
	RCC_DeInit();

	// 2. update the SystemCoreClock variable
	SystemCoreClockUpdate();

	// 3. set up peripherals
	prvSetupHardware();

	// UART
	sprintf(user_msg, "Task block APIs\r\n");
	printMsg(user_msg);

	// SEGGER
	SEGGER_SYSVIEW_Conf();
	SEGGER_SYSVIEW_Start();

	// 4. Create LED and Button Task
	xTaskCreate(
			vTask1_handler,
			"Task 1",
			500, // 500 words
			NULL,
			2,
			&xTaskHandle1
	);

	xTaskCreate(
			vTask2_handler,
			"Task 2",
			500,
			NULL,
			2,
			&xTaskHandle2
	);

	// 5. Start Scheduler
	vTaskStartScheduler();


	for(;;);
}

void vTask1_handler(void *params) {

	while(1) {
		sprintf(user_msg, "Status of the LED is %d\r\n", GPIO_ReadInputDataBit(GPIOA, GPIO_Pin_5));
		printMsg(user_msg);
		// vTaskDelay(1000); // 1000 ticks and tick rate is 1000Hz (1 ms), this line will delay 1000 ms
		vTaskDelay(pdMS_TO_TICKS(1000)); // or use the micro
	}

}

void vTask2_handler(void *params) {

	while(1) {
		GPIO_ToggleBits(GPIOA, GPIO_Pin_5);
		vTaskDelay(pdMS_TO_TICKS(1000)); // or use the micro
	}

}




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

	// 5. Enable the UART2 peripheral
	USART_Cmd(USART2, ENABLE);
}

static void preSetupGPIO(void) {

	// 1. Enable AHB1
	RCC_AHB1PeriphClockCmd(RCC_AHB1Periph_GPIOA, ENABLE);
	RCC_AHB1PeriphClockCmd(RCC_AHB1Periph_GPIOC, ENABLE);
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_SYSCFG, ENABLE); // for EXTI configuration

	// 2. Initialize GPIO
	GPIO_InitTypeDef led_init,
					 btn_init;

	// led init structure
	led_init.GPIO_Mode  = GPIO_Mode_OUT;
	led_init.GPIO_OType = GPIO_OType_PP;
	led_init.GPIO_Pin   = GPIO_Pin_5;
	led_init.GPIO_PuPd  = GPIO_PuPd_NOPULL;
	led_init.GPIO_Speed = GPIO_Medium_Speed;

	GPIO_Init(GPIOA, &led_init);

	// button init structure
	btn_init.GPIO_Mode  = GPIO_Mode_IN;
	btn_init.GPIO_Pin   = GPIO_Pin_13;
	btn_init.GPIO_PuPd  = GPIO_PuPd_NOPULL;
	btn_init.GPIO_Speed = GPIO_Medium_Speed;

	GPIO_Init(GPIOC, &btn_init);
}

static void prvSetupHardware(void) {
	// setup Button and LED
	preSetupGPIO();
	// setup UART2
	prevSetupUart();
}

void printMsg(char *msg) {

	for(uint32_t i = 0; i < strlen(msg); i++) {

		while(USART_GetFlagStatus(USART2, USART_FLAG_TXE) != SET); // waiting if transmit data register empty flag doesn't set.
		USART_SendData(USART2, msg[i]);

	}

	while(USART_GetFlagStatus(USART2, USART_FLAG_TC) != SET);

}



void vApplicationIdleHook(void) {
	// Go to sleep mode for power saving
	__WFI();
}

```

