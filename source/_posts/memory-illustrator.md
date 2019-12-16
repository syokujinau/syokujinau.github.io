---
title: Memory Illustrator
date: 2019-01-08 22:26:38
tags:
- React
- C3.js
- IEEE754
- STM32
- ARM
categories:
- Web
thumbnail: https://i.imgur.com/pCDuIRu.png
---

![](https://i.imgur.com/xUicamV.png)

[<img src="https://i.imgur.com/CExsoNY.png" width="100px" />](https://syokujinau.github.io/demo/MemoryIllustrator/)

1. Image to teach user dump log file https://tinyurl.com/y4y7lcjl

Input dump.txt

```
0x200004CC  00 00 00 00 D9 AB 17 3F - 15 04 C2 BD 15 EF 43 3E .......?......C>
0x200004DC  00 00 80 3F 09 B8 D5 3E - F3 04 B5 3E 72 AD A0 3F ...?...>...>r..?
0x200004EC  D7 B3 5D 3F 5E 83 EC 3E - B2 E4 A8 3F 8D 65 97 3F ..]?^..>...?.e.?
0x200004FC  00 00 00 3F 8D 65 97 3F - B2 E4 A8 3F 5E 83 EC 3E ...?.e.?...?^..>
0x2000050C  D7 B3 5D 3F 72 AD A0 3F - F3 04 B5 3E 09 B8 D5 3E ..]?r..?...>...>
0x2000051C  00 00 80 3F 15 EF 43 3E - 15 04 C2 BD D9 AB 17 3F ...?..C>.......?
0x2000052C  00 00 00 80 D9 AB 17 BF - 15 04 C2 3D 15 EF 43 BE ...........=..C.
0x2000053C  00 00 80 BF 09 B8 D5 BE - F3 04 B5 BE 72 AD A0 BF ............r...
0x2000054C  D7 B3 5D BF 5E 83 EC BE - B2 E4 A8 BF 8D 65 97 BF ..].^........e..
```
<!-- more -->
2. plot `outputAry` with C3.js
    * https://c3js.org/samples/simple_xy.html

## What is MemoryIllustrator?

* This is a tool to plot float array from a memory segment of ARM STM32 Series board.
* Just for fun :P
* Useless



![](https://i.imgur.com/3osDdvx.png)




