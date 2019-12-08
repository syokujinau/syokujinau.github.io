---
title: Arduino LoRa Shield通訊實驗與NodeRED
date: 2018-11-15 08:05:08
tags:
- IoT
- LoRa
- Raspberry Pi
- NodeRED
- MQTT
categories:
- IoT
thumbnail: https://i.imgur.com/gYUb3E9.jpg
---



# HW4

## 1.分別針對不同Spreading Factor情況下LoRa在移動下（可騎承腳踏車、搭公車、搭捷運、搭貓纜）所傳送資料的狀況做記錄（例：Packet Error Rate）

> 頻率設定位置: setFrequency() in the file: Sketchbook\libraries\RadioHead\RH_RF95.cpp;
> Line 114: setFrequency(918.7);

```cpp
typedef enum
    {
	Bw125Cr45Sf128 = 0,	   ///< Bw = 125 kHz, Cr = 4/5, Sf = 128chips/symbol, CRC on. Default medium range
	Bw500Cr45Sf128,	   ///< Bw = 500 kHz, Cr = 4/5, Sf = 128chips/symbol, CRC on. Fast+short range
	Bw31_25Cr48Sf512,	   ///< Bw = 31.25 kHz, Cr = 4/8, Sf = 512chips/symbol, CRC on. Slow+long range
	Bw125Cr48Sf4096,          ///< Bw = 125 kHz, Cr = 4/8, Sf = 4096chips/symbol, CRC on. Slow+long range
    } ModemConfigChoice;
```
<!-- more -->

### source code

**myLoraServerSFtest-FieldTrailTx.ino** 
```cpp
#include <SPI.h>
#include <RH_RF95.h>

RH_RF95 rf95;

long counter=0;
void setup() {    
  Serial.begin(9600);
  delay(1000);
  while (!Serial) ; // Wait for serial port to be available
  if (!rf95.init())
    Serial.println("init failed"); 
  Serial.println();
  Serial.println("Lora init passed");  
  
  rf95.setModemConfig(RH_RF95::Bw125Cr48Sf4096);  // failed
  //  rf95.setModemConfig(RH_RF95::Bw125Cr45Sf128);   // test OK (Default medium range)
  //  rf95.setModemConfig(RH_RF95::Bw500Cr45Sf128);   // test OK (Fast + short range)
  //  rf95.setModemConfig(RH_RF95::Bw31_25Cr48Sf512); // test OK (Slow + long range適用長距離)
  
  rf95.setTxPower(23, false);
}

void loop() {
  counter++;
  String numString = "Server: "+ String(counter, DEC)+"         ";  //加空白以去除亂碼
  
  uint8_t data[14] = ""; 
  for (int i=0; i<14; i++) {
   data[i] =numString[i];  //     
  }   
  rf95.send(data, sizeof(data));
  rf95.waitPacketSent();
  Serial.println(numString);
  delay(1000);
}
```

**myLoraClientSFtest-FieldTrailRx.ino** 
```cpp 
#include <SPI.h>
#include <RH_RF95.h>
#include <Wire.h>
RH_RF95 rf95;
int counter=0;

void setup() 
{
  Serial.begin(9600);
  while (!Serial) ;
  if (!rf95.init())
    Serial.println("init failed");
  Serial.println("init passed"); 
  
  rf95.setModemConfig(RH_RF95::Bw125Cr48Sf4096); 
  // rf95.setModemConfig(RH_RF95::Bw125Cr45Sf128); 
  // rf95.setModemConfig(RH_RF95::Bw500Cr45Sf128); 
  // rf95.setModemConfig(RH_RF95::Bw31_25Cr48Sf512);
  rf95.setTxPower(23, false);
}

void loop()
{
 
   if (rf95.available())
  {  
    uint8_t buf[14];
    uint8_t len = sizeof(buf);
    if (rf95.recv(buf, &len))
    {
      counter++;
      Serial.print("Rx: ");
      Serial.println((char*)buf);
      Serial.print("RSSI: ");
      Serial.println(rf95.lastRssi(), DEC);
    }
  }
}
```

## 2.不同長度的展頻symbol數，會造成不同的傳送完成時間，請量測不同Spreading Factor傳送完成所需時間紀錄。

### source code

```cpp
#include <SPI.h>
#include <RH_RF95.h>

RH_RF95 rf95;

long counter=0;
void setup() {    
  Serial.begin(9600);
  delay(1000);
  while (!Serial) ; // Wait for serial port to be available
  if (!rf95.init())
    Serial.println("init failed"); 
  Serial.println();
  Serial.println("Lora init passed");  
  
  rf95.setModemConfig(RH_RF95::Bw125Cr48Sf4096);  // failed
  //  rf95.setModemConfig(RH_RF95::Bw125Cr45Sf128);   // test OK (Default medium range)
  //  rf95.setModemConfig(RH_RF95::Bw500Cr45Sf128);   // test OK (Fast + short range)
  //  rf95.setModemConfig(RH_RF95::Bw31_25Cr48Sf512); // test OK (Slow + long range適用長距離)
  
  rf95.setTxPower(23, false);
  
  //傳送1100筆資料
  for(int j = 0; j <= 1100; j ++) {
    counter++;
    String numString = "Server: "+ String(counter, DEC)+"         ";  //加空白以去除亂碼
    
    uint8_t data[14] = ""; 
    for (int i=0; i<14; i++) {
     data[i] =numString[i];  //     
    }   
    rf95.send(data, sizeof(data));
    rf95.waitPacketSent();
    Serial.println(numString);
  }
}

void loop() {}
```



**RPi Serial monitor with timestamp**
```python
import time
from pytz import timezone # pip install pytz
from datetime import datetime
import serial

ser = serial.Serial('/dev/ttyACM0', 9600)


while True:
    u = datetime.now(timezone('Asia/Taipei'))
    print(u.strftime('%H:%M:%S.%f')[:-3] + " >> " + ser.readline())
```
![](https://i.imgur.com/UsF3nJs.png)

> 使用樹莓派讀取serial好處是可以有準確的unix timestamp，只要有行動電源與手機(AP)就能到戶外進行實測，並直接將結果存成log.txt檔案放在RPi


### 參考
* timestamp 樹莓派 https://stackoverflow.com/questions/10997577/python-timezone-conversion
* pytz-time-zones.py : https://gist.github.com/heyalexej/8bf688fd67d7199be4a1682b3eec7568
* RadioHead API : https://www.airspayce.com/mikem/arduino/RadioHead/classRHGenericDriver.html#a0feda1f5522522dc50e0c26dcdef71dd


# HW5

PM2.5 測試程式
```ino
int dustPin=0; //A0
float dustVal=0;
 
int delayTime=280;
int delayTime2=40;
float offTime=9680;

void setup(){
  Serial.begin(9600);
  pinMode(dustPin, INPUT);
}
 
void loop(){
  delayMicroseconds(delayTime);
  dustVal = analogRead(dustPin); 
  delayMicroseconds(delayTime2);
  delayMicroseconds(offTime);
   
  delay(1000);
  if (dustVal>36.455)
  Serial.println((float(dustVal/1024)-0.0356)*120000*0.035);
}
```

**LoRa Client(TX) 傳輸PM2.5資料**
```ino
#include <SPI.h>
#include <RH_RF95.h>

RH_RF95 rf95;
int count = 0;

// PM2.5
int dustPin=0; //A0
float dustVal=0;
float AirQ;
String AirQT;

void setup() {    
  Serial.begin(9600);
  pinMode(dustPin, INPUT);
  delay(1000);
  while (!Serial) ; // Wait for serial port to be available
  if (!rf95.init())
    Serial.println("init failed"); 
  Serial.println();
  Serial.println("Lora init passed Bw31_25Cr48Sf512" );  

  rf95.setModemConfig(RH_RF95::Bw125Cr45Sf128);   // test OK (Default medium range)

  
  rf95.setTxPower(23, false); 
   
}

void loop() {
    count++;
    dustVal = analogRead(dustPin);  
    PM25Info(&AirQ, &AirQT);
    
    String numString = (String)count + "," + (String)AirQ + "," + AirQT + "         ";  //加空白以去除亂碼
    
    uint8_t data[14] = ""; 
    for (int i=0; i<14; i++) {
     data[i] =numString[i];  //     
    }   
    rf95.send(data, sizeof(data));
    rf95.waitPacketSent();
    Serial.println(numString);

    delay(1000);
}

void PM25Info(float* aq, String* aqt) {
  if (dustVal>36.455)
      *aq = float((dustVal/1024)-0.0356)*120000*0.035;


  if (*aq < 300)                    { *aqt=" Good      ";}
  if (*aq >= 300 && *aq < 1050)     { *aqt=" Moderate";}
  if (*aq >= 1050 && *aq < 3000)    { *aqt=" Unhealthy";}
  if (*aq > 3000)                   { *aqt=" Hazardous";}
}
```

**LoRa Server(RX) 接收PM2.5資料**
```ino
#include <SPI.h>
#include <RH_RF95.h>
RH_RF95 rf95;


void setup() {
  Serial.begin(9600);
  while (!Serial) ;
  if (!rf95.init())
    Serial.println("init failed");
  Serial.println("init passed  Bw125Cr45Sf128"); 
  rf95.setModemConfig(RH_RF95::Bw125Cr45Sf128); 
  
  rf95.setTxPower(23, false);
}

void loop(){
   if (rf95.available()) {  
    uint8_t buf[14];
    uint8_t len = sizeof(buf);
    if (rf95.recv(buf, &len))
    {
      Serial.print("Rx: ");
      Serial.println((char*)buf);
      Serial.print("RSSI: ");
      Serial.println(rf95.lastRssi(), DEC);
    }
  }
}
```

![](https://i.imgur.com/c6rcJUr.png)

## NodeRED 環境資料整合Gateway

### MQTT 
* broker : rpi
* publisher: rpi
* subscriber: PC

### 由serial顯示LoRa Server(RX) 接收的PM2.5數值

![](https://i.imgur.com/mkg3Ofl.png)
![](https://i.imgur.com/S6HDHfj.png)

### 解析payload並mqtt publish

**nodes:**
![](https://i.imgur.com/gdBkD32.png)

**serial node:**
![](https://i.imgur.com/PIn2uK5.png)
**data switch**
![](https://i.imgur.com/L3Jkkcb.png)

**function node(parse pm2.5):**
![](https://i.imgur.com/N60CCwl.png)

**function node(parse rssi):**
![](https://i.imgur.com/UqYTMox.png)


**mqtt node (pm2.5):**
![](https://i.imgur.com/wuLY1MZ.png)

**mqtt node (RSSI):**
![](https://i.imgur.com/QnOv8Hl.png)




### 接收topic: home/LoRa的subscriber程式

```js=
var mqtt = require('mqtt')
var client  = mqtt.connect('mqtt://192.168.0.105') //broker ip (rpi)
var client2 = mqtt.connect('mqtt://192.168.0.105')  
 
client.on('connect', function () {
  console.log('mqtt broker connected!');
  client.subscribe('home/LoRa/pm2.5');
})
 
client.on('message', function (topic, msg) {
    let info = JSON.parse(msg)
    console.log('\n收到pm2.5 topic: ' + topic);
    console.log(`時間: ${info.time} \npm2.5: ${info.pm25} \nstatus: ${info.status}`);
})

// for rssi 
client2.on('connect', function () {
    console.log('mqtt broker connected!');
    client2.subscribe('home/LoRa/rssi');
  })
   
client2.on('message', function (topic, msg) {
    let info = JSON.parse(msg)
    console.log('\ntopic: home/LoRa/rssi')
    console.log("RSSI: " + info.rssi)
})
```

![](https://i.imgur.com/wHloW1P.png)


## GUI

> 需先安裝node-red-dashboard
> Manage palette > install > 輸入node-red-dashboard

**nodes:**
![](https://i.imgur.com/TheFU1s.png)

**get pm2.5 value function**
![](https://i.imgur.com/of2EayZ.png)


![](https://i.imgur.com/PSyyF0h.png)

![](https://i.imgur.com/adW7r7z.png)


## Publish溫溼度(DHT22)資料 

```cpp=
#include <SPI.h>
#include <Ethernet.h>
#include <PubSubClient.h>
#include "DHT.h"

#define DHTPIN 2     // what digital pin we're connected to
#define DHTTYPE DHT22   // DHT 22  (AM2302), AM2321
DHT dht(DHTPIN, DHTTYPE);

// set MAC address
static byte mac[] = { 0xF0, 0x7B, 0xCB, 0x4B, 0x7C, 0x9F };
// set server and UNO IP adderss 
IPAddress ip(192, 168, 0, 108);
IPAddress server(192, 168, 0, 105); // broker ip

// Client ID
const char clientID[] = "uno";
// topic
const char topic[] = "home/dht22";

String msgStr = "";

char json[25];

EthernetClient ethClient;        // Ethernet object
PubSubClient client(ethClient);  // MQTT object basedon Ethernet

void setup(){
  Serial.begin(9600);
    
  dht.begin();
    
  client.setServer(server, 1883); //broker and port
  Ethernet.begin(mac, ip);        // initialize Ethernet

  delay(1500);
}

void loop(){
  if (!client.connected()) {
    reconnect();
  }
  
  client.loop(); // update MQTT client
  
  float h = dht.readHumidity()
  float t = dht.readTemperature();// Read temperature as Celsius (the default)
 
  // JSON format string
  msgStr = msgStr + "{\"temp\":" + (String)t + ",\"humid\":" + (String)h + "}";
  // turn to char array, store in json
  msgStr.toCharArray(json, 25);
  
  client.publish(topic, json);
 
  msgStr = ""; // clean
  
  delay(5000);
}
```

加溫溼度的nodes
![](https://i.imgur.com/kGZEpaM.png)


