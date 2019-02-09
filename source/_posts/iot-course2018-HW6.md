---
title: 透過AWS IoT core儲存sensor資料至DynamoDB
date: 2018-11-26 11:15:28
tags:
- AWS
- IoT
- Python
- matplotlib
- DynamoDB
thumbnail: https://i.imgur.com/9b82heZ.jpg
---

> 這是物聯網導論HW6，使用AWS IoT core與物聯網裝置經由MQTT通訊，儲存到DynamoDB，再由後端使用matplotlib繪製感測器資訊。

![](https://i.imgur.com/FyLh6ax.png)


# 入門

照著步驟做，先安裝`pip install AWSIoTPythonSDK`

![](https://i.imgur.com/Srd2tGj.png)






# AWS IoT subscribe PM2.5 MQTT

## 修改政策
IOT CORE -> 安全無虞 -> 政策 -> {政策的名稱} -> 編輯政策文件 

![](https://i.imgur.com/hBu7nDY.png)
![](https://i.imgur.com/PAsU1EQ.png)

<!-- more -->

## 發布、訂閱程式 

```python=
# basicPubSub_PM2.5.py
from AWSIoTPythonSDK.MQTTLib import AWSIoTMQTTClient
from serial import Serial
import logging
import time
from pytz import timezone # pip install pytz
from datetime import datetime
import argparse
import json

AllowedActions = ['both', 'publish', 'subscribe']

# Custom MQTT message callback
def customCallback(client, userdata, message):
    print("Received a new message: ")
    print(message.payload)
    print("from topic: ")
    print(message.topic)
    print("--------------\n\n")

def publish(PM25):
    if mode == 'both' or mode == 'publish':
	u = datetime.now(timezone('Asia/Taipei'))
        message = {}
        message['type'] = 'PM2.5'
        message['value'] = PM25
        message['time'] =u.strftime('%H:%M:%S')

        messageJson = json.dumps(message)
        myAWSIoTMQTTClient.publish(topic, messageJson, 1)
        if mode == 'publish':
            print('Published topic %s: %s\n' % (topic, messageJson))

host = "a1edmqmbezxdwv-ats.iot.us-east-1.amazonaws.com"
rootCAPath = "./root-CA.crt"
certificatePath = "./RPi.cert.pem"
privateKeyPath = "./RPi.private.key"
useWebsocket = False
clientId = "vensen"
topic = "RPi/PM25"
mode = "publish"


# Port defaults
if useWebsocket:  # When no port override for WebSocket, default to 443
    port = 443
if not useWebsocket:  # When no port override for non-WebSocket, default to 8883
    port = 8883

# Configure logging
logger = logging.getLogger("AWSIoTPythonSDK.core")
logger.setLevel(logging.DEBUG)
streamHandler = logging.StreamHandler()
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
streamHandler.setFormatter(formatter)
logger.addHandler(streamHandler)

# Init AWSIoTMQTTClient
myAWSIoTMQTTClient = None
if useWebsocket:
    myAWSIoTMQTTClient = AWSIoTMQTTClient(clientId, useWebsocket=True)
    myAWSIoTMQTTClient.configureEndpoint(host, port)
    myAWSIoTMQTTClient.configureCredentials(rootCAPath)
else:
    myAWSIoTMQTTClient = AWSIoTMQTTClient(clientId)
    myAWSIoTMQTTClient.configureEndpoint(host, port)
    myAWSIoTMQTTClient.configureCredentials(rootCAPath, privateKeyPath, certificatePath)

# AWSIoTMQTTClient connection configuration
myAWSIoTMQTTClient.configureAutoReconnectBackoffTime(1, 32, 20)
myAWSIoTMQTTClient.configureOfflinePublishQueueing(-1)  # Infinite offline Publish queueing
myAWSIoTMQTTClient.configureDrainingFrequency(2)  # Draining: 2 Hz
myAWSIoTMQTTClient.configureConnectDisconnectTimeout(10)  # 10 sec
myAWSIoTMQTTClient.configureMQTTOperationTimeout(5)  # 5 sec

# Connect and subscribe to AWS IoT
myAWSIoTMQTTClient.connect()
if mode == 'both' or mode == 'subscribe':
    myAWSIoTMQTTClient.subscribe(topic, 1, customCallback)
time.sleep(2)

# Publish to the same topic in a loop forever
ser = Serial('/dev/ttyACM0', 9600)

while True:
	str = ser.readline().decode('utf8')[:-2]
	end = str.find('\r')
	PM25 = str[:end]
	publish(PM25)
	time.sleep(2)
```

> 放在與start.sh同一層路徑下
![](https://i.imgur.com/Rfj2hnX.png)


Host在start.sh內(Line 21)
```sh=
# stop script on error
set -e

# Check to see if root CA file exists, download if not
if [ ! -f ./root-CA.crt ]; then
  printf "\nDownloading AWS IoT Root CA certificate from AWS...\n"
  curl https://www.amazontrust.com/repository/AmazonRootCA1.pem > root-CA.crt
fi

# install AWS Device SDK for Python if not already installed
if [ ! -d ./aws-iot-device-sdk-python ]; then
  printf "\nInstalling AWS SDK...\n"
  git clone https://github.com/aws/aws-iot-device-sdk-python.git
  pushd aws-iot-device-sdk-python
  python setup.py install
  popd
fi

# run pub/sub sample app using certificates downloaded in package
printf "\nRunning pub/sub sample application...\n"
python aws-iot-device-sdk-python/samples/basicPubSub/basicPubSub.py -e a3l21ss4gwlsqf-ats.iot.us-east-1.amazonaws.com -r root-CA.crt -c pi3.cert.pem -k pi3.private.key
```




![](https://i.imgur.com/jsFY4f9.png)

```python=
from AWSIoTPythonSDK.MQTTLib import AWSIoTMQTTClient
from serial import Serial
import logging
import time
from pytz import timezone # pip install pytz
from datetime import datetime
import argparse
import json
import random

AllowedActions = ['both', 'publish', 'subscribe']

# Custom MQTT message callback
def customCallback(client, userdata, message):
    print("Received a new message: ")
    print(message.payload)
    print("from topic: ")
    print(message.topic)
    print("--------------\n\n")

def publish(PM25):
    if mode == 'both' or mode == 'publish':
        u = datetime.now(timezone('Asia/Taipei'))
        message = {}
        message['time'] = u.strftime('%H:%M:%S')
        message['value'] = PM25
        messageJson = json.dumps(message)
        myAWSIoTMQTTClient.publish(topic, messageJson, 1)
        if mode == 'publish':
            print('Published topic %s: %s\n' % (topic, messageJson))

host = "a3l21ss4gwlsqf-ats.iot.us-east-1.amazonaws.com"
rootCAPath = "./root-CA.crt"
certificatePath = "./RPi.cert.pem"
privateKeyPath = "./RPi.private.key"
useWebsocket = False
clientId = "myRPi"
topic = "RPi/PM25"
mode = "publish"


# Port defaults
if useWebsocket:  # When no port override for WebSocket, default to 443
    port = 443
if not useWebsocket:  # When no port override for non-WebSocket, default to 8883
    port = 8883

# Configure logging
logger = logging.getLogger("AWSIoTPythonSDK.core")
logger.setLevel(logging.DEBUG)
streamHandler = logging.StreamHandler()
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
streamHandler.setFormatter(formatter)
logger.addHandler(streamHandler)

# Init AWSIoTMQTTClient
myAWSIoTMQTTClient = None
if useWebsocket:
    myAWSIoTMQTTClient = AWSIoTMQTTClient(clientId, useWebsocket=True)
    myAWSIoTMQTTClient.configureEndpoint(host, port)
    myAWSIoTMQTTClient.configureCredentials(rootCAPath)
else:
    myAWSIoTMQTTClient = AWSIoTMQTTClient(clientId)
    myAWSIoTMQTTClient.configureEndpoint(host, port)
    myAWSIoTMQTTClient.configureCredentials(rootCAPath, privateKeyPath, certificatePath)

# AWSIoTMQTTClient connection configuration
myAWSIoTMQTTClient.configureAutoReconnectBackoffTime(1, 32, 20)
myAWSIoTMQTTClient.configureOfflinePublishQueueing(-1)  # Infinite offline Publish queueing
myAWSIoTMQTTClient.configureDrainingFrequency(2)  # Draining: 2 Hz
myAWSIoTMQTTClient.configureConnectDisconnectTimeout(10)  # 10 sec
myAWSIoTMQTTClient.configureMQTTOperationTimeout(5)  # 5 sec

# Connect and subscribe to AWS IoT
myAWSIoTMQTTClient.connect()
if mode == 'both' or mode == 'subscribe':
    myAWSIoTMQTTClient.subscribe(topic, 1, customCallback)
time.sleep(2)

# Publish to the same topic in a loop forever
ser = Serial('/dev/ttyACM0', 9600)

while True:
        str = ser.readline().decode('utf8')[:-2]
        end = str.find('\r')
        PM25 = str[:end]
        publish(PM25)

```

# 在VM執行 fetch DynamoDB 

## 基本連線

```python=
import boto3
from boto3.dynamodb.conditions import Key

dynamodb = boto3.resource('dynamodb', region_name='us-east-1',
                                      aws_session_token='FQoGZXIvYXdzEIz//////////wEaDEmGC67DXliftdi15yLjAlNe8Meiqw9LjtH0bS2WCsEwKrtZdGVj4lEXx7YlsON2f3I6vQ4FRqen8d+hT/zUpVzDb6JVMblgslHXSUlaQWi8qovFVQtbw/AvtdlSiZlH3nENUGpev5rlVe30dl5DAiFj8HIZV47+zIcsfZXRqkL8faMpnssTCNqVRwcF4iX5U9o6vjHvMVYelW+EnUVU9/Le2bTe5QDcUsvhAOvzzZ4ZlE/SFEYfFQ4/W3cbr83NLEvU4Bqj8bqKu8L2Vmotbcong/3SFe/QgJH6KsN5Q28HDnjtnvGxFf3z2v/lGOV9e+qVWNFht5/nh6dk+JXni59hXibZa7LIZxHKAOVqs+Vk8es9JVUqm4EYH16e5fHqHLG9gSPJG64RVBa8Jth1hCjWryAlitWFdk1FTiuHdsLYIBRuPz54sdFFJFGoudEnC7/C/8eZPO+H0JseakCFZNMv9zm92cXYoJ9bS/eIu10s0Ykoi8iJ4AU=',
                                      aws_access_key_id = 'ASIA27E3PJ447BBJPJNA',
                                      aws_secret_access_key = 'taQBfl4HxZF6xyzQvflv2BT04p5sy6dMwKyeo1CE'
                          )

response = table.get_item( Key = {'time': '14:00:01'}) # 取出特定時間資料
try:
    # print(response)
    item = response['Item']
    message = item['message']
    print(message['value'])
except KeyError:
    print('')
```

## source code

取出DB資料，存成log.txt
```python=
import boto3
from boto3.dynamodb.conditions import Key

dynamodb = boto3.resource('dynamodb', region_name='us-east-1',
                                      aws_session_token='FQoGZXIvYXdzEIz//////////wEaDEmGC67DXliftdi15yLjAlNe8Meiqw9LjtH0bS2WCsEwKrtZdGVj4lEXx7YlsON2f3I6vQ4FRqen8d+hT/zUpVzDb6JVMblgslHXSUlaQWi8qovFVQtbw/AvtdlSiZlH3nENUGpev5rlVe30dl5DAiFj8HIZV47+zIcsfZXRqkL8faMpnssTCNqVRwcF4iX5U9o6vjHvMVYelW+EnUVU9/Le2bTe5QDcUsvhAOvzzZ4ZlE/SFEYfFQ4/W3cbr83NLEvU4Bqj8bqKu8L2Vmotbcong/3SFe/QgJH6KsN5Q28HDnjtnvGxFf3z2v/lGOV9e+qVWNFht5/nh6dk+JXni59hXibZa7LIZxHKAOVqs+Vk8es9JVUqm4EYH16e5fHqHLG9gSPJG64RVBa8Jth1hCjWryAlitWFdk1FTiuHdsLYIBRuPz54sdFFJFGoudEnC7/C/8eZPO+H0JseakCFZNMv9zm92cXYoJ9bS/eIu10s0Ykoi8iJ4AU=',
                                      aws_access_key_id = 'ASIA27E3PJ447BBJPJNA',
                                      aws_secret_access_key = 'taQBfl4HxZF6xyzQvflv2BT04p5sy6dMwKyeo1CE'
                          )

## utiliy function ####################################
def toTimeStamp(u):
    HH = str(u/3600) if u/3600 >= 10 else '0' + str(u/3600);
    u %= 3600;
    MM = str(u/60) if u/60 >= 10 else '0' + str(u/60);
    SS = str(u%60) if u%60 >= 10 else '0' + str(u%60);
    return HH + ':' + MM +  ':' + SS;


def toUnixTime(time):
    t = time.split(':');
    i = 3600;
    sum = 0;
    for a in t:
        sum += int(a) * i;
        i /= 60;
    return sum;

def getMoveAvgBy30secRange(u1): # trace back 30 sec and calculate move average
    res = [];
    u2 = u1 - 30;

    for i in range(u1, u2, -1):
        print('>>' + toTimeStamp(i))
        response = table.get_item(Key={'time': toTimeStamp(i)})
        try:
            item = response['Item']
            message = item['message']
            res.append(message['value']);
        except KeyError:
            print('')
    sum = 0;
    for x in res:
        sum += x;
    moveAvg = sum / len(res);
    return {toTimeStamp(u1) : moveAvg};
#####################################################

table = dynamodb.Table('PM25sensor')


# setting
start_hour = '15:00:00';
total = 1;

list_30sec_avg = [];
list_2min__avg = [];
list_10min_avg = [];

f = open("log.txt", "a")

# list of 30 sec move avg
t1 = toUnixTime(start_hour);
end = toTimeStamp(t1 - total * 3600);
t2 = toUnixTime(end);
print(start_hour + 'trace back to '+ end)
while(t1 != t2): #
    list_30sec_avg.append(getMoveAvgBy30secRange(t1));
    print(list_30sec_avg);
    t1 -= 30;

print(list_30sec_avg);
f.write(str(list_30sec_avg));


```

# 產生移動平均chart

```python=
import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
from scipy import stats


f = open('log.txt', 'r')

data = f.read().split('{');

data = data[1:];

# print(data[1:])

moveAvg_30sec = [];
timeList = [];
pm25List = [];


for e in data:
    # print(e)
    time = e[1:9];
    timeList.append(time)
    # print(time);
    s = e.split("': Decimal('");
    pm25 = s[1].split("'")[0]
    pm25_float = float(pm25)
    pm25List.append(pm25_float)
    moveAvg_30sec.append({time: pm25});

# visualization

df = pd.DataFrame({'time':timeList, 'pm2.5': pm25List})
# df['pm2.5'] = df['pm2.5'].astype('float64') # convert pm2.5 string to float
df['index_col'] = df.index;

print(df.head(5));

fig, ax = plt.subplots(1,1)
ax.set_xticklabels(df['time'], rotation=15, fontsize=18)
sns.lineplot(x="index_col", y="pm2.5", data=df)
plt.title("30 sec move average")
# plt.show()







##### 2 min move avg

# clear list
timeList = [];
pm25List = [];

moveAvg_2min = []
for i in range(0, len(moveAvg_30sec) - len(moveAvg_30sec)%4, 4):
    time = moveAvg_30sec[i].keys()[0];
    avg2 = (float(moveAvg_30sec[i].values()[0]) + float(moveAvg_30sec[i + 1].values()[0]) + float(moveAvg_30sec[i + 2].values()[0]) + float(moveAvg_30sec[i + 3].values()[0])) / 4;
    moveAvg_2min.append({time: avg2});

# print (moveAvg_2min)
for e in moveAvg_2min:
    # print(e)
    time = e.keys()[0];
    timeList.append(time)
    pm25 = e.values()[0];
    pm25_float = float(pm25)
    pm25List.append(pm25_float)


# visualization

df = pd.DataFrame({'time':timeList, 'pm2.5': pm25List})
# df['pm2.5'] = df['pm2.5'].astype('float64') # convert pm2.5 string to float
df['index_col'] = df.index;

print(df.head(5));

fig, ax = plt.subplots(1,1)
ax.set_xticklabels(df['time'], rotation=15, fontsize=18)
sns.lineplot(x="index_col", y="pm2.5", data=df)
plt.title("2 min move average")
plt.show()







##### 10 min move avg

# clear list
timeList = [];
pm25List = [];

moveAvg_10min = []
for i in range(0, len(moveAvg_30sec) - len(moveAvg_30sec)%20, 20):
    time = moveAvg_30sec[i].keys()[0];
    avg10 = (float(moveAvg_30sec[i].values()[0]) + float(moveAvg_30sec[i + 1].values()[0]) + float(moveAvg_30sec[i + 2].values()[0]) + float(moveAvg_30sec[i + 3].values()[0]) + float(moveAvg_30sec[i + 4].values()[0]) + float(moveAvg_30sec[i + 5].values()[0]) + float(moveAvg_30sec[i + 6].values()[0]) + float(moveAvg_30sec[i + 7].values()[0]) + float(moveAvg_30sec[i + 8].values()[0]) + float(moveAvg_30sec[i + 9].values()[0]) + float(moveAvg_30sec[i + 10].values()[0]) + float(moveAvg_30sec[i + 11].values()[0]) + float(moveAvg_30sec[i + 12].values()[0]) + float(moveAvg_30sec[i + 13].values()[0]) + float(moveAvg_30sec[i + 14].values()[0]) + float(moveAvg_30sec[i + 15].values()[0]) + float(moveAvg_30sec[i + 16].values()[0]) + float(moveAvg_30sec[i + 17].values()[0]) + float(moveAvg_30sec[i + 18].values()[0]) + float(moveAvg_30sec[i + 19].values()[0])) / 20;
    moveAvg_10min.append({time: avg10});


for e in moveAvg_10min:
    # print(e)
    time = e.keys()[0];
    timeList.append(time)
    pm25 = e.values()[0];
    pm25_float = float(pm25)
    pm25List.append(pm25_float)


# visualization

df = pd.DataFrame({'time':timeList, 'pm2.5': pm25List})
# df['pm2.5'] = df['pm2.5'].astype('float64') # convert pm2.5 string to float
df['index_col'] = df.index;

# print(df.head(5));

fig, ax = plt.subplots(1,1)
ax.set_xticklabels(df['time'], rotation=15, fontsize=18)
sns.lineplot(x="index_col", y="pm2.5", data=df)
plt.title("10 min move average")
plt.show()
```

![](https://i.imgur.com/abWzFwS.png)

![](https://i.imgur.com/4wplaVo.png)

![](https://i.imgur.com/DOuzfkS.png)

