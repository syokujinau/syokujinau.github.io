---
title: CVSD 2018 Fall Nodebook
date: 2018-11-12 14:56:09
tags:
- Verilog
---






## Verilog基本語法

### Operators
1. **Bitwise Operators**(每個bit都會做): `~`(invert)  
加在兩變數中間變數之前 `&`,`|`,`^`(XOR),`~^`(XNOR)    ex:`~m`,`m&n`
2. **Unary Operators** (加在變數之前，對自己的每個bit做運算，縮至一個bit)
`&`,`~&`,`|`,`~|`,`^`,`~^`  ex: `&m`
3. **Arithmetic Operators**: +,-(放在兩個數字中間當兩數相減、放在一個數字前面代表對他取二補數),(無號數的乘法),`/`,`%`(取餘數，這個指令不可合成) ex: `m+n`
4. **Logic Operators**: `!`(not),`&&`,`||` ex:`!m`
5. **Equality Operators**(比較數值): `==`  (數值是否相等?), `!=` (does not equal?) ex: `m==n`
6. **Identity Operators**(不可合成，只用在testbench): `===` (是否等價?多考慮X、Z的狀態), `!==`  (是否不相同) ex: `m===n`
7. **Relational Operators**: `<`,`>`,`<=`,`>=`
8. **Logical Shift Operators**: `<<`,`>>` ex: `m<<2`，代表m左移兩個bits，相當於4(補0)
9. **Misc Operators**: `條件?為真的值:為偽的值`(三元運算子),`{m,n}`(串接),
 `{n{m}}`(m重複n次)


**Procedural Block**: 出現`always@`或`initial `
<!-- more -->
**Initial block**: 在testbench中給初值

**always@**: 當條件改變時，總是會做...
1. 用於需要FF時  
posedge/negedge:正/負緣觸發，若reset看clk為synchronous，不看為asynchronous
```verilog=
//下列為asynchronous
always@(posedge clk, negedge reset)
```

2. 用於需要條件判斷時(合成MUX)
出現在Procedure Block等號左邊的變數(output)要宣告成reg，因為這變數會因為某些條件改變而改變，所以需要存一段時間，但這不代表需要FF

**Assignment**
Continuous assignment:在procedural block的外面，用assign的方式給值，output要宣告成wire
**Procedural assignment**:在procedural block中給值，output要宣告成reg，又分為blocking(用`=`，做完才做其他的事)或non-blocking(用`<=`，會先把右手邊都做完，等到跳出block時，一起把值吐到左手邊去)
```verilog=
a = b;
b = a; 
// 結果是 a=a 
```

```verilog=
b <= a; 
a <= b;  
// 結果是b update成 a的舊值，a update成 b的舊值 (swap)
```

```verilog=
CLR=#5 1; 
CLR=#4 0; 
// 結果是一行一行做 
```

```verilog=
CLR<=#5 1; 
CLR<=#4 0; 
// 結果是按照時間順序
```


Logic分成combinational(根據不同的輸入產生不同的輸出，可用讓它自己找需要的參數)和sequential logic(還會跟之前的狀態有關，看sensitive list來合FF，不能用)

邏輯判斷
case...default...
```verilog=
case(判斷條件) 
	8’b1:
      …
    default: 異常時，易判斷問題出在哪
endcase
```
if...else...
```verilog=
if (判斷條件) ...
else if(判斷條件) ...
else ...
```

如果條件沒寫滿會不知道該怎麼辦，只好保持不動，產生latch
如果要告訴合成軟體說這就是所有會發生的情形，可以如下 
`Case(判斷條件) // synopsys fullcase`

## Ch1

![p.17](https://i.imgur.com/D1ppIxm.png)

module name不要取數字，不要用特殊符號

![p.21](https://i.imgur.com/647Fezh.png)



![p.23](https://i.imgur.com/kiCJb7E.png)

![p.32](https://i.imgur.com/m07Qnvu.png)



![p.45](https://i.imgur.com/3mczgZS.png)


![p.46](https://i.imgur.com/cwjuS0F.png)

![p.48](https://i.imgur.com/XOG3GCr.png)

![p.49](https://i.imgur.com/BAOQxTC.png)

![](https://i.imgur.com/6abzsAk.png)

變數前面的是vector，代表wordlength
變數後面的是number，代表有幾個


![p.57](https://i.imgur.com/eQ6i0pk.png)


![p.58](https://i.imgur.com/JctdmG4.png)

![p.60](https://i.imgur.com/j8Jf7K3.png)

預設ns


![p.82](https://i.imgur.com/P17fIxb.png)

![p.83](https://i.imgur.com/fHohXFd.png)




## Ch2


![p.25](https://i.imgur.com/swBaWkX.png)

tri-state buffer盡量不要用，合成和P&R時會錯

![p.26](https://i.imgur.com/E78fWA1.png)

優先順序由上到下

![p.27](https://i.imgur.com/3COlSQd.png)


unsized constant在沒宣告時預設32 bits

![p.40](https://i.imgur.com/WV54FDL.png)

combinational loop可簡化logic but CAD tool不支援，因為會被視為asynchronous而不斷執行，很難去預測答案

![p.44](https://i.imgur.com/km7rKGl.png)


![p.47](https://i.imgur.com/QGy5ssA.png)

![p.49](https://i.imgur.com/L2w2Iqo.png)

![p.54](https://i.imgur.com/dmfHuh5.png)

always @ block用在
1.邏輯判斷
2.希望合成出DFF(Sequential circuit)





![p.55](https://i.imgur.com/2TNxPgC.png)

![p.58](https://i.imgur.com/c7GbKZQ.png)

![p.86](https://i.imgur.com/8PCXjyf.png)

![p.87](https://i.imgur.com/3NSW46F.png)

![p.88](https://i.imgur.com/HzVRpbW.png)

![p.89](https://i.imgur.com/gX9xa6x.png)

![p.98](https://i.imgur.com/OL5ocoo.png)



## Ch3



![p.4](https://i.imgur.com/CKQczvG.png)


<img src="https://i.imgur.com/QkZ1ylz.png" width="60%">

timing包含兩種
(1)delay:元件的特性

(2)clock period: 有多少的時間給我運算(兩個FF中間有多少時間)

根據clock period的timing constraint，會去調整standard cell的size，以符合timing的限制
ex:希望加法器變快，會把standard cell的size加大，面積加大之後電路操作的速度會變快，delay會變小，就比較有機會符合clock period的規格

所以圖中講的speed會隨delay而異
但由於clock period的限制，我們可能會調整面積去meet timing constraint，所以某種程度上，圖中的speed也會隨著調整clk period而改變


![p.13](https://i.imgur.com/1ikQEwl.png)

跟don't care比較永遠永遠都會是錯的

![p.16](https://i.imgur.com/XWsThi0.png)

![p.18](https://i.imgur.com/FZQm0nj.png)

從design compiler得到的timing，是寬鬆的設計，如果要測量電路可以跑多快，可以一直把timing constraint(sdc檔中)往下降，直到出現negative slack為止

在所有的輸出加register比較好，這樣可以確保三個電路會在同一個時間內算完，這樣電路會比較好做測試，因為從波形上可以看到穩定的數字，不會出現glitch，但需要看的點再加register就好，因為這會讓latency變差

![p.19](https://i.imgur.com/tWOklxt.png)

DC不會optimize電路，只會optimize元件，如果沒做pipeline，電路可能會來不及算完
現在的tool會自動幫忙移動FF，只要把設計寫對之後加FF，tool就會做retiming


![p.20](https://i.imgur.com/Jsw85Is.png)

把critical path 和其他電路分開，才能分別optimize

![p.22](https://i.imgur.com/rgvDDQn.png)


![p.23](https://i.imgur.com/U1pMLSC.png)

![p.27](https://i.imgur.com/xna9MEz.png)

如果case沒寫滿會合出latch，這易受noise影響，因為latch是combinational的電路，只要輸入改變，輸出就會跟著改變



[p.37]
<img src="https://i.imgur.com/LSSx6m9.png" width="60%">

[p.38]
<img src="https://i.imgur.com/pWZXwmb.png" width="60%">

signed會告訴compiler，MSB代表signed bit

![p.39](https://i.imgur.com/nXuyeMT.png)

轉成signed的三種可能方式
(1)宣告時加`signed`
ex: `wire signed [8:0] c`;
(2)在tb的`$display()`內，變數前加一個`$signed`
(3)在tb的`$display()`內，數字前加一個s
ex: `9'sd1`

如果變數沒有宣告成signed bit，會預設為unsigned
比較時，若比較對象出現unsigned的比較，要轉為unsigned

**signed跟unsigned的mapping**
從0開始由小到大先寫正整數，寫完之後接著由小到大寫負數

Q:4 bits的範圍由幾到幾?
16個數字中，一個被0用掉，剩下15個分給正和負，負數會比正數多一個(因為正數有一個給0使用)
4bits => -8~+7

![p.40](https://i.imgur.com/ZqkDli7.png)


![p.42](https://i.imgur.com/byC9krT.png)

如果debug要看陣列內的訊號，要加p.42最下面的兩行指令


![p.43](https://i.imgur.com/FuivRaF.png)

![](https://i.imgur.com/kJ5fIEU.png)

如果合成後看到type有出現latch，要回頭把RTL的code改掉




[p.75]
<img src="https://i.imgur.com/SCkobJZ.png" width="60%">

圖中的`datafile`是design好要餵給testbench吃test pattern的檔案

![p.76](https://i.imgur.com/G0YMFby.png)

`@`代表值要被放到哪個address，投影片中的address是採用16進制
ex:@100 => 1×16^2^+0×16^1^+0×16^0^=256

![p.80](https://i.imgur.com/Gq1YVdB.png)

VCD(value change dump) format: 所有訊號轉變的波形檔，檔案較大
FSDB(fast signal database) format: 會做編碼和去掉不重要的，檔案較小
如果要看二維陣列的訊號，要加`+mda`，這是所有memory波形檔



[p.84]
<img src="https://i.imgur.com/E4G7etZ.png" width="60%">


post-sim時要include sdf中，這樣才會看時間有沒有violate
sdf檔是在合成之後，會記錄時間資訊的檔案




## Ch4



![p.4](https://i.imgur.com/fjajOcI.png)

Latency是一個data進來到完成運算所需要的時間
Throughput是單位時間內可完成的運算量

![p.6](https://i.imgur.com/F4yCJTN.png)

pipeline是插FF，如果在一個path插一個FF，切一刀之後delay不會變一半，因為插入FF的set-up time也要考慮

![p.11](https://i.imgur.com/xNjvBiy.png)

電路的資料流是有方向性的，要確保cut-st上資料的流向要一致

![p.15](https://i.imgur.com/bRr2Rpw.png)

多組元件平行進行，增加了Throughput，但要確保input能夠吃下這樣的loading

![p.17](https://i.imgur.com/WtBPHVJ.png)

![p.19](https://i.imgur.com/d3zzHQF.png)

Retiming: tool會自動讓cell在FF間做移動



![p.23](https://i.imgur.com/9qUmN74.png)



![p.25](https://i.imgur.com/2iKV4Cl.png)

N~node~: node的數量
C~L~: 後端tool才能optimize
V^2^~dd~: 這受限於製程，不能改
只能調整clock的頻率

![p.27](https://i.imgur.com/U29hSnW.png)

以下的方法可以減少Power Consumption
(1)讓電路正常運作就好
(2)不要用的時候會關掉clk
(3)平常沒在運作時可以讓clk的速度變慢
(4)做不重要的動作時，可以把電壓降低
(5)減少不必要的切換，例如counter數完時，讓他停住不要再跳




![p.61](https://i.imgur.com/Uf3SKSV.png)

glitch發生時，可以用FF去擋

![p.68](https://i.imgur.com/dSlRDDv.png)





## Ch5-1


| **考試重點**  |
|-----------------|
| set-up time: 這一筆資料不能太慢抵達 <br/> FF在trigger時，存到rigister前，不能改變(因為clk不會立刻跳，電路不能跑太快) <br/> T~clk~ > T~CLKtoQ~ + T~data~(combinational) + T~setup~ - T~skew~  <br/> hold time: 下一筆的資料不能太快來，否則會data feedthrogh，tool會自動插delay修正 <br/> T~hold~ < T~CLKtoQ~ + T~data~ - T~skew~ <br/> ![](https://i.imgur.com/g6ItnCB.png)  <br/> [STA Link](https://www.cnblogs.com/poiu-elab/archive/2012/10/29/2745390.html)|

> set-up time 的T~CLKtoQ~是從clk起來，一直到出現stable的輸出所需要的時間  
> hold time 的T~CLKtoQ~是從clk起來，一直到出現輸出所需要的時間，若要更嚴謹地不被下一筆資料做影響，下一筆的輸出可以是**unstable**的
> 另外set-up time 要取最長的path，而hold time要取最短的path做檢查


成因：因為clock不是理想的，寫到FF內的暫存器時，不是一瞬間的 => set-up time。
set-up time: 這一筆資料被寫到暫存器所需要的時間，所以這一筆資料不能來的太慢(slow.db是用來確認setup time)。
成因：因為clock不是理想的，第一級的暫存器開關不會立刻關掉，下一筆資料如果來太快，會被寫入暫存器內部，所以資料需要維持一段時間不能變動 => hold time
hold time: 下一筆資料來之前所需要維持的時間，所以下一筆資料不能來的太快(fast.db是用來確認有無hold time violation)。

set-up time 在檢查時要取==最慢==的path.
hold time 要取==最快==的path.

**Q:如何解決violation**
**A:** set-up time : 做retiming(調整FF的位置)，平均分攤每兩級FF之間的combinational logic time 或者是調大clock。
hold time : 插delay buffer。
直到滿足兩個不等式為止。



skew time：因走線有寄生電容，線越長Clock越晚到，所以相較之下，Data會來得比較快
這一筆資料來得比較快，對setup time有利，所以不等式是用加的。
下一筆資料也來的比較快，所以對hold time不利，所以不等式是用減的。

[Clock Skew: Implication on Timing](http://vlsi-soc.blogspot.com/2013/03/clock-skew-implication-on-timing.html)


clock skew又稱作clock uncertainty，clock因為繞線以及layout等於因導致抵達各個register時的時間不一。
但是在**STA階段無法準確知道哪個register收到的clock是快的或是慢的**，所以design compiler在分析static timing analysis時，在分析setup time或hold time時必須一律以**worst case**來做分析。
"邏輯所能使用之clock period皆減少"
T~CLKtoQ~ + T~data~(combinational) + T~setup~ ==+== T~skew~
T~hold~ < T~CLKtoQ~ + T~data~ - T~skew~

<br/>

| **考試重點**  |
|-----------------|
|Technology library:`.db`(給**人**看)、`.lib`(給**機器**看)，紀錄在不同操作環境下的cell的timing的資訊，提供給`dc(design compiler)`做**合成** <br/>    PVT:Process Voltage Temperature <br/> typical.db:中速製程 <br/> `slow.db`:慢速製程，分析**set-up time** <br/> `fast.db`:快速製程，分析**hold-time** <br/> CPU跑超頻時=>**加大電壓**或**加液態氮(降溫)**、電流取決於mobility、threshold voltage，會決定電路的操作速度而μ、V~t~隨溫度改變的趨勢不同，並不是一直降溫都會有用 |

design compiler會把.db和.lib互轉



[p.6]
<img src="
" width="60%">
<img src="https://i.imgur.com/xvvBjEp.png" width="80%">

Design Ware Library 像合成的函式庫
HDL compiler會吃進RTL Design和Design Ware Library，轉成high level的gate level(例如:一群FF、MUX、加法器、乘法器、ALU)
Design compiler會吃進Design constraint和HDL Compiler給他的檔案，還有Design Ware Library給他的，輸出Optimized Gate-level Netlist，這個步驟才有吃進process的資訊


**檔案的整理**

**Design Ware Library** 像合成的函式庫
HDL compiler會吃進RTL Design和Design Ware Library，轉成high level的gate level(例如:一群FF、MUX、加法器、乘法器、ALU)
Design compiler會吃進Design constraint和HDL Compiler給他的檔案，還有Design Ware Library給他的，輸出Optimized Gate-level Netlist，這個步驟才有吃進process的資訊

**.db / .lib**: Technology library:`.db`(給**人**看)、`.lib`(給**機器**看)，紀錄在不同操作環境下的cell的timing的資訊，提供給`dc(design compiler)`做**合成**用

**.synopsys_dc.setup**:設定環境的library，提供給DC做合成
ex:設定要去哪個(search) path尋找SRAM或I/O pad的cell、還有target (technology) library 為哪個操作環境的db，有設定才會認得SRAM和I/O pad，才會在area report反映在Macro的面積

**.sdc檔**(synopsys design constraints)  ，記載一些合成前的設定，提供給DC做合成
裡面有的內容如clk的period給多長來操作，clk長怎樣、不明朗的範圍多大、hold time多少、哪些block不要動且要設為ideal、一些會影響delay的設定、操作環境為何、set maximal area constraint、cost是以什麼考量為優先的、有沒有設定wire load model、線的推動力怎樣、input/output delay多大

> .sdc、main.tcl都是屬於自動化的腳本
> 以上都是提供給DC做合成用，以下是合成完後要提供給ncverilog做模擬用


**_syn.v**(Gate level netlist): 根據DC compiler吃的.sdc、HDL compiler提供的high gate level netlist、designware library的參數去合成gate level的netlist，提供給ncverilog做模擬用

**.sdf**(Standard Delay format)，紀錄一些時間的資訊，給ncverilog做模擬用
應該是根據合成前.sdc中的設定，萃取出一些的時間資訊，例如input delay、output delay、wire load model的delay為何、hold time為多少，然後提供給ncverilog做模擬

**tsmc13.v**(Process Library):提供合成後verilog netlist map 到 cell的 behavior 之後timing的資訊，提供給ncverilog做模擬用

**.ddc**:DC合成完後會儲存的工作檔

[p.7]
<img src="https://i.imgur.com/gK60zk2.png" width="80%">

[p.13]
<img src="https://i.imgur.com/kr13lvv.png" width="80%">
`.synopsys_dc.setup` 環境設定的library，讀取DV和DC的指令然後開始執行，一定要跟DV/DC檔在同一個資料夾





[p.13]
<img src="https://i.imgur.com/kr13lvv.png" width="80%">
[p.15]
<img src="https://i.imgur.com/P9K0zte.png" width="80%">


.gds layout




| **考試重點** |
| ---- |
| 可以把要做的事情寫成文字檔(script)，這會跟開dv按按鍵的步驟都有對應<br/>P&R要用script跑，通常在synthesis叫出GUI的機會不高，除非要找critical path去修negative slack，或者是要看合出來的東西是否正確 
```tcl=
# main.tcl
# Import Design
read_file -format verilog  "./CLE.v"

current_design [get_designs CLE]
link

source -echo -verbose ./CLE_DC.sdc

# Compile Design
current_design [get_designs CLE]

set high_fanout_net_threshold 0

uniquify
set_fix_multiple_port_nets -all -buffer_constants [get_designs *]

compile 

# Output Design
current_design [get_designs CLE]

remove_unconnected_ports -blast_buses [get_cells -hierarchical *]

set bus_inference_style {%s[%d]}
set bus_naming_style {%s[%d]}
set hdlout_internal_busses true
change_names -hierarchy -rule verilog
define_name_rules name_rule -allowed {a-z A-Z 0-9 _} -max_length 255 -type cell
define_name_rules name_rule -allowed {a-z A-Z 0-9 _[]} -max_length 255 -type net
define_name_rules name_rule -map {{"\\*cell\\*" "cell"}}
define_name_rules name_rule -case_insensitive
change_names -hierarchy -rules name_rule

write -format ddc     -hierarchy -output "CLE_syn.ddc"
write -format verilog -hierarchy -output "CLE_syn.v"
write_sdf -version 2.1  CLE_syn.sdf
```





| **考試重點**  |
|-----------------|
|名詞定義 <br/> <img src="https://i.imgur.com/HlSKk5t.png" width="80%"> <br/> 看圖 <br/><img src="https://i.imgur.com/0MgTupN.png" width="80%"> <br/> <img src="https://i.imgur.com/TYBj201.png" width="80%"> |


![p.36](https://i.imgur.com/CceleXN.png)



[p.40]
<img src="https://i.imgur.com/eo2dZiH.png" width="60%">


## Ch5-2



![p.3](https://i.imgur.com/6FSfyDE.png)


也可以把上面的指令寫在`.sdc`檔

`dc_shell> set_clock_uncertainty 0.1 [get_clocks clk]`


![p.4](https://i.imgur.com/k2Dhnpm.png)

skew會讓data來得相較clock來說比較快，這一筆資料來得快，對set-up time有利，下一筆資料來得快，對hold time不利

[p.5]
<img src="https://i.imgur.com/7wtt08e.png" width="80%">

clock uncertainty會讓能算的時間下降


![p.9](https://i.imgur.com/liFwbpc.png)

false path代表這條路徑不可能會發生，要了解電路的樣子才能設，這是要DC不要檢查這條路徑


* Clock gating
Low power design
EN=1時為一般clk trigger的FF；EN=0時，module被gating掉，不會操作
Enable訊號一定是透過logic來的，可能會有glitch
tool合法化了一個clk gating cell，由Enable控制module是否會被打開



![p.10](https://i.imgur.com/DhqVHmG.png)

clk gating cell有兩種合成的方法，楊老師Lab推薦第一種作法
1. FF中，寫if而沒有else，在合成時會利用latch cell把module給關掉
2. assign glk= clk & EN ， EN=0時，clk不會跳，但要在dc_shell中輸入指令，確保被換成clk gating cell


area與process有關，算gate count時，要把logic都normalize成nand gate或nor gate


![p.11](https://i.imgur.com/tE6A3qA.png)

dc_shell> set_max_area 0
希望達到的目標
最佳化問題有目標函數和限制函數，我們希望area越小越好，所以目標可以直接訂成0，DC會努力幫我們去壓面積，因為這不是我們的限制，所以不用擔心會過不了
但有時候壓面積會讓timing constraint過不了，兩者常常互相衝突



![p.12](https://i.imgur.com/yFdA9a1.png)

這一頁投影片這個與Physical design的DRC不同
除了timing以外，這些參數的設定也會影響到delay的演算法
因為delay的算法是透過standard cell calibre在某個區間，透過查表算出來的，有一個較準的範圍，所以要讓電路操作在校準的環境

![p.13](https://i.imgur.com/AJU5VDd.png)

一般我們performance先看timing，再看area，之後才會看power有多大
可以用 set_cost_priority來自訂他们的優先權

![p.15](https://i.imgur.com/WyGN3qJ.png)

設dont_touch: RTL就不會在動
可能已經有額外利用DC花了很多時間把某些block給合成好，不希望DC再去更動合成好的block

![p.16](https://i.imgur.com/1dTnp0K.png)

這會把module間的邊界打破，可以做到跨模組的優化，但會花很多時間，在職場上time to market也很重要

![p.17](https://i.imgur.com/01l6ChF.png)

這比較少用，可以針對每個實體，create一個design，大家會有各自的counter

**以上的步驟都是寫在.sdc檔中**


![p.24](https://i.imgur.com/nQH6mBp.png)

如果出現負的slack，代表電路跑太慢，要切pipeline或者回去修正RTL的code

![p.25](https://i.imgur.com/HbXYHQa.png)

圖中的橫軸是slack的長度，縱軸是path的數量
(1)設計要盡量讓所有的path都靠近0，切pipeline要把大家的時間切得差不多
(2)調整完之後再改變.sdc檔中的clk cycle，把clk往下壓
(3)重複(1)、(2)的動作直到不能再把clk壓下去為止
(4)如果不知道怎麼切pipeline，有最後一招可以去解slack，但記得這一招只能用一次
可以插一堆registers之後，下`optimize_register`的指令，讓DC幫我們優化

> optimize後的名字可能會看不懂，因為會產生很多設計以外的東西，且design會比較大(area buffer cost)

![p.26](https://i.imgur.com/sfEGSwr.png)

有set wire load model時，會有Net Interconnect area和Total area(Total cell area + Net Interconnect area)，這兩項不用考慮，我們只要負責優化Combinational area(adder,乘法器、MUX...)和Noncombinational area(Sequential FF)
Macro/Black Box area指得是memory的面積 ex:SRAM的面積



![p.30](https://i.imgur.com/hyt9zmB.png)

合成時就要考慮I/O pad
模擬時要有I/O pad的behavior檔

![p.32](https://i.imgur.com/lssl4Vx.png)


slow.lib是給standard cell吃的環境
tpz013g3wc.lib是外圍pad
rom_1024×4_t13_slow_syn.lib是memory的library，裡面包含timing、power的資訊，透過DC可以轉成.db檔給機器看

![p.33](https://i.imgur.com/1r3V9h6.png)

![p.34](https://i.imgur.com/sFMRbwq.png)

![p.35](https://i.imgur.com/M7y1nwN.png)

HW3中的CLE_DC.sdc

![p.36](https://i.imgur.com/gAKfHAo.png)

.ddc檔是關design compiler之後的工作站存檔

![p.37](https://i.imgur.com/ZVFbNO4.png)


![p.38](https://i.imgur.com/1c0ZbYK.png)

![p.39](https://i.imgur.com/wZ3JDWJ.png)

tsmc13.v是 CORE的behavior model，是用來跑模擬用的

![p.40](https://i.imgur.com/l2B5DQL.png)

![p.41](https://i.imgur.com/onO2AHK.png)















## Ch6



![p.28](https://i.imgur.com/USwcuag.png)

Fault Coverage是偵測出fault的數量與所有可能出現fault數量的比值

[p.29]
<img src="https://i.imgur.com/q4WwoEz.png" width="80%">

and的特性，兩個輸入中若有一個是0，結果應該是0；若兩個輸入都是1，結果應該是1，當違反and gate的原則，就代表有錯誤，那這個fault就能夠被看到
測試的兩大重點
1. Activate: 輸入的pattern要能夠讓fault被反映出來(選與fault相反的值，藉由這個特性可以回推部分test pattern該有的值)
2. 
3. Propagate: fault要能夠反映到輸出，不能被其他的logic gate擋下來(藉由這個特性可以回推其他test pattern該有的值)

![](https://i.imgur.com/bhfnVlX.png)

應用and gate 和 or gate的天性，我們可以在activate fault之後，做implication，推回test pattern應該為多少





如下圖，stuck at fault 就是將一 input 卡在錯誤的值，看 output 是否能夠被偵測到<br/>(與原先 pattern 應得結果不同)，若有偵測到，表示 detect fault。

<img src="https://i.imgur.com/q4WwoEz.png" width="80%">

以下圖為例，<br/>
1. s-a-0(D) => 我們首先要能 activate 它，就必須假設它為1，<br/>再往回推要得到1所需要的條件(A=1、B=1)。<br/>
2. 為了能將錯誤表現出來，我們希望它能影響到 output 的值，也就是       propagation，<br/>為了能將 0 的錯誤推至 output 需要 E=1 ，E=0 的話 D 不論為 0、1 都無法表現。

<img src="https://i.imgur.com/yndfYpL.png" width="80%">

(P.29 與 P.39 為考試重點)

<img src="https://i.imgur.com/LIBynic.png" width="80%">
<img src="https://i.imgur.com/xjC35qx.png" width="80%">
<img src="https://i.imgur.com/11Qa5Uz.png" width="80%">

下圖也是依照兩個原則<br/>
1. activation : 7 條 wire 可以插 stuck at fault 。<br/>
2. propagation : 看看你插的 fault 會不會改變 output 原先 pattern 正確的值。<br/>
注意 : 只需考慮 single stuck at fault ，一次只考慮一個 fault，其他假設都正確。


<img src="https://i.imgur.com/5gMYUjY.png" width="80%">
<!--
<img src="https://i.imgur.com/ApmoGHR.png" width="80%">

<img src="https://i.imgur.com/42PRYYf.png" width="80%">

<img src="https://i.imgur.com/HZd3Mnd.png" width="80%">

<img src="https://i.imgur.com/pHyG6YB.png" width="80%">

<img src="https://i.imgur.com/xAHgVyD.png" width="80%">
-->


## Ch7

![p.4](https://i.imgur.com/PVObFvX.png)


![p.10](https://i.imgur.com/tLWZ8kz.png)

四種data path
Start Point可能是Clock pin(CLK port)或者是PI(primary input)
End Point可能是PO(primary output)或者是data input pin(D)
所以會有四種可能的Path

CLK port到FF的data input(D)
CLK port到PO
PI到FF的data input(D)
PI到PO

![p.11](https://i.imgur.com/wAUJba4.png)

有幾個Start points?
PI: A,B,C有三個
CLK port有四個
共七個

有幾個End points?
PO:O,Q,P有三個
FF的data input port有四個
共七個

有幾條timing path?
底下圖中紅色的1+1+3+1+4+4+2=16


![p.15](https://i.imgur.com/ABNlrGY.png)

<考>
到這個點為止需要多少時間
由前面往後算
所有要花的時間取最大值
每個edge都可以定義delay，delay的累積就是arrival time
multifanin要取大的delay，例如:中間NOR gate的input分別為0.65和0.7，要取比較慢的，所以是0.7+0.2


![p.16](https://i.imgur.com/f0c18SF.png)

<考>
這個點還有多少時間可以使用
所有剩下的時間取最小值
Spec.的要求
要接到後面的電路，會被要求多少時間之前資料必須要被準備好
由後面往前算
希望PO的vertex在時間1.5的時候要收到資料
由後往前推時，如果有兩個以上的剩下時間，要取比較小的，再繼續往前把每一段需要的時間一直消耗，就可以算出每個vetex的required time是多少

![p.17](https://i.imgur.com/AXHiprE.png)

<考>
required time - arrived time 可以得到slack graph
如果slack是負的，就是critical path，要讓slack>0，才不會有timing violation


![p.26](https://i.imgur.com/WFFU2F2.png)


![p.32](https://i.imgur.com/VDBrtqf.png)

兩種方式造成clock latency
1. off chip cause: 時脈產生器到晶片過程造成的delay
機台上有石英震盪器，到晶片的時間model出的行為
2. on chip cause: 線長不同、擺置線寬不同，造成sequential吃到的clk不同

![p.33](https://i.imgur.com/aw2dOzo.png)

取最大的跟最小的相減，代表clk可能會浮動的範圍
就算使用H tree的擺法
thermal noise造成clk到每一顆FF的時間略有不同
波型起來的地方可能會早一點或晚一點到
下去的地方可能早一點或晚一點下去
這些可以靠tool模擬出來

![p.36](https://i.imgur.com/ljyiXjn.png)

哪些在設計中是false path，在SAT計算中永遠不會發生
電路中的某個path平常不會被操作到
ex:reset的block平常不會被觸發
ex:有個訊號不再被需要了(input vector永遠不會再觸發某一條path的功能)，別的vertex看那一條會是constant
ex:這個path不會再影響其他node甚至PO
ex:combinational loop不應該在電路中發生，所以被設為false(DC目前還沒有辦法做分析)
cf.跟之前所說的，set false path 故意設定false讓DC compiler不要去估timing analysis，不太一樣

![p.46](https://i.imgur.com/LbQYYpF.png)

<SOCV有教>
setup-time 吃到combinational circuit的訊號後sequential circuit需要花一段時間才會把值propagate到output，必須要把這些結果計算起來
圖中沒事是On time
前面propagate太久壓縮到setup time

這一筆資料在path上的值不可以來得太慢，是剩下的時間扣掉實際到達的時間


![p.51](https://i.imgur.com/UQa7wwX.png)

![p.52](https://i.imgur.com/luckFaQ.png)

Hold time: combinational circuit算出值之後要被Sequenial circuit寫入，所以不能太快改變

反過來減，下一筆資料，經過path不可以算得太快，值要維持得夠久，所以是arrival time減掉required time

![p.56](https://i.imgur.com/yIDZ2Ug.png)






## 以下為考古的問答題



**問答1: interconnect area如果是0，如何解決??**
就是沒設wire load，在.sdc檔　set_wire_load_model　有關

`Net Interconnect area`是0: 在set design constraint(.sdc)檔案中，如果沒有設定`set wire load model`，那cell間的走線面積就不會考慮，所以`interconnect area`會是0
`set_wire_load_model -name tsmc13_wl10 -library slow `


```sdc=
# CLE_DC.sdc
# You can only modify clock period 

set cycle  10         ;#clock period defined by designer

#don't modify the following part
create_clock -period $cycle [get_ports  clk]
set_dont_touch_network      [all_clocks]
set_fix_hold                [all_clocks]
set_clock_uncertainty  0.1  [all_clocks]
set_clock_latency      0.5  [all_clocks]
set_ideal_network           [get_ports clk]

#Don't touch the basic env setting as below
set_input_delay  1     -clock clk [remove_from_collection [all_inputs] [get_ports clk]]
set_output_delay 1     -clock clk [all_outputs] 
set_load         1     [all_outputs]
set_drive        1     [all_inputs]

set_operating_conditions -max_library slow -max slow
set_wire_load_model -name tsmc13_wl10 -library slow                        

set_max_fanout 6 [all_inputs]
```



![](https://i.imgur.com/sD0Luza.png)

**問答2: 為什麼`Macro/Black Box area`是0?**
因為環境設定檔中，`.synopsys_dc.setup`內的SRAM路徑不對，DC找不到SRAM的library，認不得所以沒吃到Macro的面積
`search_path `會去搜尋Macro cell的gate netlist path在哪裡
`target_library `是放置Macro cell 的technology library的path在哪裡，透過`target_library `，我們會把`link_library` link到Macro的cell，這樣就能認得Macro的cell

```
# .synopsys_dc.setup
set search_path      ". /home/raid7_2/course/cvsd/CBDK_IC_Contest/CIC/SynopsysDC/db  ./Memory/sram_1024x8_t13 $search_path"
set target_library   "slow.db                     \
					  typical.db
					  fast.db
                      sram_1024x8_t13_slow_syn.db \
                     "
set link_library     "* $target_library dw_foundation.sldb"
```

```
# .synopsys_dc.setup
# 1. Modify this file to fit your own environment.
# 2. Copy this file synopsys_dc.setup to .synopsys_dc.setup
#    and put this file in tool's invoking directory or your home directory.
#	

set company {NTUGIEE}
set designer {Student}

set search_path      ". /home/raid7_2/course/cvsd/CBDK_IC_Contest/CIC/SynopsysDC/db  ./Memory/sram_1024x8_t13 $search_path"
set target_library   "slow.db                     \
					  typical.db
					  fast.db
                      sram_1024x8_t13_slow_syn.db \
                     "
set link_library     "* $target_library dw_foundation.sldb"
set symbol_library   "tsmc13.sdb generic.sdb"
set synthetic_library "dw_foundation.sldb"
set default_schematic_options {-size infinite}

set hdlin_translate_off_skip_text "TRUE"
set edifout_netlist_only "TRUE"
set verilogout_no_tri true

set hdlin_enable_presto_for_vhdl "TRUE"
set sh_enable_line_editing true
set sh_line_editing_mode emacs
history keep 100
alias h history

set bus_inference_style {%s[%d]}
set bus_naming_style {%s[%d]}
set hdlout_internal_busses true
define_name_rules name_rule -allowed {a-z A-Z 0-9 _} -max_length 255 -type cell
define_name_rules name_rule -allowed {a-z A-Z 0-9 _[]} -max_length 255 -type net
define_name_rules name_rule -map {{"\\*cell\\*" "cell"}}

```


**問答3: timing report是讓critcal path可以過timing的constraints，
如果過了5ns的constraints，把testbench改成4ns使否有機會meet constraints?**
有機會，如果測資走的不是critical path，那就有機會通過限制

**問答4: Why we only focus on total cell area instead of total area?**
因為我們能夠掌握的只有RTL的design，而他會影響的面積是Cominational、Buffer、Non-combinational的area，wire的面積不是我們能決定的，而且這個面積在P&R之後才會比較準確

**問答5: Why the total cell area will be underestimated if we don't set wire load model?**
因為沒有設定wire model，那就不會考慮wire的delay，所以timing constraints會比較好通過
若今天把wire delay考慮回來，那timing constraints的條件會變得比較嚴苛，所以cell的面積必須要加大，讓操作速度變得比較快，才能通過constraints

**問答6: 如果今天把.sdc中的clock period設小，area會怎麼改變?**
由於clock period變小，timing constraints的條件變嚴苛，需要更大的硬體面積才能通過timing的限制，所以area會變大
(area越大，delay越小)


**問答7: 我們需要synthesis rom_1024×4_t13.v with DUT.v(我們設計的module)嗎?**
不用，因為.v檔已經有提供ncverilog模擬timing delay的資訊，不需要再合成
另外我們已經有合成後的.gds檔(layout檔)，所以不用再合成
