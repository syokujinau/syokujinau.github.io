---
title: Nachos - 教學用作業系統
date: 2019-08-27 19:21:17
tags:
- Operating System
categories:
- Miscellaneous
thumbnail: https://i.imgur.com/K75CZHO.png
---


Nachos作為教學用作業系統,需要實現簡潔並且儘量縮小與實際作業系統之間的差距,適合作為作業系統課程的教學實踐平臺。Nachos 是加州大學伯克萊分校在作業系統課程中已多次使用的作業系統課程設計平臺,在國內外很多大學中得到了應用,它在作業系統教學方面具有以下幾優點:

## 1. 採用通用虛擬機器

Nachos 是建立在一個軟體類比的虛擬機器之上的,模擬了 MIPS R2/3000 的指令集、主記憶體、中斷系統、網路以及磁片系統等作業系統所必須的硬體系統。許多現代作業系統大多是先在用軟體類比的硬體上建立並測試,最後才在真正的硬體上運行。用軟體類比硬體的可靠性比真實硬體高得多,不會因為硬體故障而導致系統出錯,便於測試。虛擬機器可以在運行時報告詳盡的出錯資訊,更重要的是採用虛擬機器使 Nachos 的移植變得非常容易,在不同機器上移植 Nachos,只需對虛擬機器部分作移植即可。

採用 R2/3000 指令集的原因是該指令集為 RISC 指令集,其指令數目比較少。Nachos虛擬機器類比了其中的 63 條指令。由於 R2/3000 指令集是一個比較常用的指令集,許多現有的編譯器如 gc++能夠直接將 C 或 C++來源程式編譯成該指令集的目標代碼,於是就不必編寫編譯器,讀者就可以直接用 C/C++語言編寫應用程式,使得在 Nachos 上開發大型的應用程式也成為可能。

## 2. 使用並實現了作業系統中的一些新的概念

隨著電腦技術和作業系統技術的不斷發展,產生了很多新的概念。Nachos 將這些新概念融入作業系統教學中,包括網路、執行緒和分散式應用。而且 Nachos 以執行緒作為一個基本概念講述,取代了行程在以前作業系統教學中的地位。Nachos 的虛擬機器使得網路的實現相當簡單。與 MINIX 不同,Nachos 只是一個在宿主機上運行的一個行程。在同一個宿主機上可以運行多個 Nachos 行程,各個行程可以相互通訊,作為一個全互連網路的一個節點;行程之間通過 Socket 進行通訊,類比了一個全互連網路。

<!-- more -->

## 3. 確定性測試比較方便;隨機因素使系統運行更加真實

因為作業系統的不確定性,所以在一個實際的系統中進行多執行緒測試是比較困難的。由於 Nachos 是在宿主機上運行的行程,它提供了確定性測試的手段。所謂確定性測試,就是在同樣的輸入順序、輸入參數的情況下,Nachos 運行的結果是完全一樣的。在多執行緒測試中,可以將注意力集中在某一個實際問題上,而不受作業系統不確定性的干擾。另外,不確定性是作業系統所必須具有的特徵,Nachos 採用了隨機因數類比了真實作業系統的不確定性。

## 4. 簡單而易於擴展

Nachos 是一個教學用作業系統平臺,它必須簡單而且有一定的擴展餘地。Nachos 不是向讀者展示一個成功的作業系統,而是讓讀者在一個框架下發揮自己的創造性進行擴展。例如一個完整的類似於 UNIX 的檔案系統是很複雜的,但是對於檔案系統來說,無非是需要實現檔的邏輯位址到物理位址的映射以及實現檔 inode、打開檔結構、執行緒打開檔表等重要的資料結構以及維護它們之間的關係。Nachos 中具有所有這些內容,但是在很多方面作了一定的限制,比如只有一級索引結構限制了系統中最大檔的大小。讀者可以應用學到的各種知識對檔案系統進行擴展,逐步消除這些限制。Nachos 在每一部分給出很多課程作業,作為讀者進行系統擴展的提示和檢查對系統擴展的結果。

## 5. 物件導向性

Nachos 的主體是用 C++的一個子集來實現的。目前物件導向語言日漸流行,它能夠清楚地描述作業系統各個部分的介面。Nachos 沒有用到物件導向語言的所有特徵,如繼承性、多態性等,所以它的代碼就更容易閱讀和理解。


## Nachos的實驗環境


### 所需軟體 

Install g++ and csh
```shell=
sudo apt-get install g++
sudo apt-get install csh
sudo apt-get install make
```

Download NachOS & Cross Compiler
```shell=
curl -O http://homepage.ntu.edu.tw/~r07943154/files/OS2019/nachos-4.0.tar.gz
curl -O http://homepage.ntu.edu.tw/~r07943154/files/OS2019/mips-decstation.linux-xgcc.gz
```



### 安裝

untar **nachos-4.0.tar.gz**

```shell=
tar -xvf nachos-4.0.tar.gz
```

move **mips-decstation.linux-xgcc.gz** to root and untar

```shell=
sudo mv mips-decstation.linux-xgcc.gz /
cd /
sudo tar -zxvf mips-decstation.linux-xgcc.gz
```


Make NachOS-4.0
```shell=
cd ~/nachos-4.0/code
make
```



### 測試

```shell=
cd ./userprog
./nachos -e ../test/test1
```


```
Total threads number is 1
Thread ../test/test1 is executing.
Print integer:9
Print integer:8
Print integer:7
Print integer:6
return value:0
```

## Nachos的目錄結構

以上作業系統可以發現在工作目錄下生成一個名為 nachos-4.0 的目錄。該目錄中含有:

| 檔名 | 類型 | 內容 |
|---|---|---|
|copyright|文件|Nachos的版權|
|readme|文件|Nachos的readme資訊|
|nachos.ps|文件|Nachos 的介紹文檔(Postscript 格式)|
|c++example|目錄|有關 C++介紹和實例|
|doc|目錄|Nachos 各個部分介紹和原有的作業要求|
|code|目錄|Nachos 各個部分的原始程式碼|


最主要的部分是 Nachos 的原始程式碼部分。它的目錄結構是:

| 檔名 | 類型 | 內容 |
|---|---|---|
|Makefile|文件|Nachos 的 Makefile 文件。當 Nachos 需要移植到其它系統時,可以修改 Makefile.dep 中的 HOST 參數|
|Makefile.common|文件| |
|Makefile.dep|文件| |
|machine|目錄|虛擬機器類比部分原始程式碼|
|threads|目錄|Nachos 執行緒管理部分原始程式碼|
|filesys|目錄|Nachos 檔案系統管理部分原始程式碼|
|userprog|目錄|Nachos 使用者程式部分原始程式碼|
|network|目錄|Nachos 網路管理部分原始程式碼|
|vm|目錄|Nachos 虛擬記憶體管理部分原始程式碼|
|test|目錄|一些測試用應用程式|
|bin|目錄|包含有使用者程式目的碼變換的程式|





## 參考

* [General Nachos Documentation](https://homes.cs.washington.edu/~tom/nachos/)
* [Wiki - Not Another Completely Heuristic Operating System](https://en.wikipedia.org/wiki/Not_Another_Completely_Heuristic_Operating_System)
* [A Road Map Through Nachos](https://users.cs.duke.edu/~narten/110/nachos/main/main.html)
* [University of Pittsburgh - NachOS](https://people.cs.pitt.edu/~manas/courses/CS1550/nachos.htm)