---
title: FreeRTOS - Task States
date: 2019-12-13 06:57:19
tags:
- Embedded
- Operating System
categories:
- [Embedded, FreeRTOS]
thumbnail: https://i.imgur.com/rjbUjJo.png
---



FreeRTOS同樣也有task state，創造task後配置記憶體空間，進入waiting list等待scheduler選取才進去CPU執行(Running state)

<!-- ![](https://i.imgur.com/N54wDal.png) -->

![](https://i.imgur.com/RkzbeQg.png)

## Blocked state

使用時機
1. 需要暫停的task透過`vTaskDelay()`來達到blocking delay
2. 行程同步，例如先讓一個負責資料處理task進入blocked state，等待data buffer被其他task或interrupt填滿，再unblock

FreeRTOS的queue、binary semaphore、counting semaphore、recursive semaphore與mutex等可以達到行程同步的功能需要做tack blocking

<!-- ![](https://i.imgur.com/jgF3Eu5.png) -->

## Suspended state

<!-- more -->

Suspended state是non—running state的一種，很少使用，task一旦進入suspended則會禁止scheduler對它進行排程，只能透過`vTaskSuspend()`來進入該狀態`vTaskResume()`來脫離並進入waiting list

<!-- ![](https://i.imgur.com/0GT1VXw.png) -->


<!-- ![](https://i.imgur.com/xmC2MVp.png) -->

## Example - Blocking Delay

<script src="https://gist.github.com/syokujinau/f5c9a8aea22a6881d3bd30d386978a88.js"></script>



