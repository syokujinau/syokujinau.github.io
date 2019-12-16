---
title: 設置BeagleBone嵌入式硬體開發環境
date: 2019-10-16 17:11:14
tags:
- Embedded Linux
- BeagleBone 
categories:
- [Embedded, Embedded Linux]
thumbnail: https://i.imgur.com/dWZ74DZ.png
---



本篇要在Host端設置開發環境，編譯執行檔，並傳送給Remote device執行並測試程式。

**Host**: 作業系統為Windows10，我們還需要IDE、Tool-chian以及Make tool
**Remote device**: BeagleBone Black

## Download

1. [Eclipse IDE for C/C++ Developers](https://www.eclipse.org/downloads/packages/)
    * [eclipse-cpp-oxygen-3a-win32-x86_64.zip](https://www.eclipse.org/downloads/download.php?file=/technology/epp/downloads/release/oxygen/3a/eclipse-cpp-oxygen-3a-win32-x86_64.zip)
    * 根據JDK是安裝32或64bit，選擇對應eclipse版本，下載後解壓縮，點及主程式選擇workspace路徑，可以用git repo儲存
3. [Cross compilation tool-chian](http://releases.linaro.org/components/toolchain/binaries/)
    * [gcc-linaro-6.3.1-2017.02-i686-mingw32_arm-linux-gnueabihf.tar.xz](http://releases.linaro.org/components/toolchain/binaries/6.3-2017.02/arm-linux-gnueabihf/gcc-linaro-6.3.1-2017.02-i686-mingw32_arm-linux-gnueabihf.tar.xz)
5. ["Make" exe file for Windows](http://gnuwin32.sourceforge.net/packages/make.htm)
    * [GnuWin](http://gnuwin32.sourceforge.net/downlinks/make.php)

<!-- more -->

## New Workspace

解壓Eclipse IDE for C/C++ Developers後執行主程式

![](https://i.imgur.com/s8UvEYU.png)

![](https://i.imgur.com/KCUM5oq.png)

![](https://i.imgur.com/YOA8QG7.png)

加入Cross compiler prefix與path (bin資料夾路徑)

![](https://i.imgur.com/zRjmOnt.png)

完成新增Project

![](https://i.imgur.com/vMmd4sA.png)


安裝make tool for windows



![](https://i.imgur.com/TL5aAzF.png)

![](https://i.imgur.com/Izmexxv.png)

![](https://i.imgur.com/IyMPDHI.png)


接著，到eclipse IDE 右鍵 build project

![](https://i.imgur.com/51wWL3Y.png)

會看到產生binary檔

![](https://i.imgur.com/blLbDwM.png)

接下來要將這個binary用Remote System Explorer傳送到BBB硬體去執行檔案，BBB就是我們的remote system


![](https://i.imgur.com/p0ijbuJ.png)


![](https://i.imgur.com/fHFXGjf.png)

接著都按Next、同意license後完成

![](https://i.imgur.com/KPp0tBW.png)

安裝完成後會需要重開IDE

## Remote SSH Terminal

![](https://i.imgur.com/gTRhmZp.png)


![](https://i.imgur.com/uK7GZgw.png)

![](https://i.imgur.com/jUVdiZz.png)

之後都按Next、Finish

![](https://i.imgur.com/A3f1jed.png)

![](https://i.imgur.com/ticJSXY.png)

之後就能看到BBB的file system

![](https://i.imgur.com/jo86dgw.png)

從host端(Windows)傳送硬體可執行檔方法很簡單，直接拖曳到BBB的檔案系統即可

![](https://i.imgur.com/rPhBzS8.png)


![](https://i.imgur.com/F3K3O3L.png)

如此就能在IDE整合SSH連線

![](https://i.imgur.com/TchsbiY.png)


* `cd Desktop/`
* 給予執行權限 `chmod +x TestProject`
* 執行 `./TestProject`


![](https://i.imgur.com/rsAdJNj.png)

完成開發環境設置，之後就能在Host開發、編譯，在傳到BBB上執行程式。


## Import Existing Projects

![](https://i.imgur.com/xT5DsZ4.png)


:::info
import project後可能需要重新新增cross compiler的絕對路徑`cc-linaro-6.3.1-2017.02-i686-mingw32_arm-linux-gnueabihf\bin`
以及設置remote ssh terminal
:::