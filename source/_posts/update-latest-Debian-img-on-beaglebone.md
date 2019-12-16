---
title: BeagleBone eMMC 更新Debian至最新版本
date: 2019-11-08 21:48:41
tags:
- Embedded Linux
- BeagleBone 
- Operating System
- Debian
categories:
- [Embedded, Embedded Linux]
thumbnail: https://i.imgur.com/mOo5sDC.jpg
---
 


![](https://i.imgur.com/CiFIvcD.png)

以下步驟將eMMC裡的舊版Debian更新成新版。

<!-- more -->

1. [下載](https://beagleboard.org/latest-images)最新Debian 
    * ![](https://i.imgur.com/S2YkDkp.png)
3. 燒錄img檔至SD卡，我使用 [balenaEtcher](https://www.balena.io/etcher/) 這個軟體來寫入SD卡
    * ![](https://i.imgur.com/RHNW5Yx.png)
3. 用 **3.3v** FTDI接上板子上的 *J1 Header*，Putty打開FTDI的COM port (baud = 115200)
    * ![](https://i.imgur.com/GilqjP5.png)
5. 從SD卡開機 
    * 未上電的狀態下插入SD卡
    * 按住板子上的S2按紐，插上電後再放開
    * 登入Debian，預設帳號debian，密碼temppwd
    * *目前只是從SD卡開機，eMMC還是舊版Debian*
    * 可看到登入時版本顯示為9.9，通常可用`lsb_release -da`指令確認，經測試8.7板可使用，9.9版本無法(?)，有待確認
6. 從SD卡執行eMMC flasher script 
    1. `cd /opt/scripts/tools/eMMC/`
    2. `sudo ./init-eMMC-flasher-v3.sh` (作者[Github](https://github.com/RobertCNelson/boot-scripts/blob/master/tools/eMMC/init-eMMC-flasher-v3.sh))
    3. 便開始將新版Debian從SD卡燒錄到eMMC，實際上做以下的事
        1. 切割eMMC記憶體為兩部分，`FAT`與`ext4`，格式化與產生檔案系統
        2. 從SD卡內容複製到新的eMMC記憶體區塊
        3. 最後會自動關機
7. 取出SD卡，重新上電
    * ![](https://i.imgur.com/0z7CMGD.png)
    * 此時eMMC已經成功更新為Debian 9.9

### 透過USB分享網路

BeagleBone Black不像Raspberry pi 3一樣有WiFi功能，所以一般使用要接Ethernet等方式上網，以下步驟在Windows環境分享目前電腦使用的網路給BeagleBone

<!-- 接上Mini USB到電腦後，可以看到一個新的 -->

![](https://i.imgur.com/Fg9iQlF.png)


![](https://i.imgur.com/fgf8OvF.png)

![](https://i.imgur.com/Kls4HOi.png)

1. 打開putty，SSH連線至`192.168.7.2`
2. 登入後，新增default gateway
    * 輸入`sudo route add default gw 192.168.7.1 usb0`
4. 加入DNS server
    * `sudo vim /etc/resolv.conf`
    * ![](https://i.imgur.com/QmzpQfB.png)

如此便能成功上網。