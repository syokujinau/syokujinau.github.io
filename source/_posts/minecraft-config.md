---
title: Minecraft server架設&設定檔紀錄
date: 2018-06-21 10:01:06
tags:
- Minecraft
- Google Compute Engine
- Linux
thumbnail: https://i.imgur.com/qJbMWEQ.png
---


> ShiginimaSE_v3100 (破解版 請自己找)
> Adderss: yc-minecraft.tk  (已關閉)

## [Setting Up a Minecraft Server on Google Compute Engine](https://cloud.google.com/solutions/gaming/minecraft-server)

> 官方已提供很詳盡的說明，架完後可用[freenom](https://www.freenom.com/zu/index.html?lang=zu)的免費域名(最長1年免費)，就不用記IP。

## Info
* 版本:1.12.2
* 地圖類型: 大型生態系
* pvp: on
* 作弊: off
* 人數:20
<!-- more -->
* server.properties
```
#Minecraft server properties
#Fri Jun 29 04:31:51 UTC 2018
max-tick-time=60000
generator-settings=
force-gamemode=false
allow-nether=true
gamemode=0
enable-query=false
player-idle-timeout=0
difficulty=2
spawn-monsters=true
op-permission-level=4
pvp=true
snooper-enabled=true
level-type=LARGEBIOMES
hardcore=false
enable-command-block=false
max-players=20
network-compression-threshold=256
resource-pack-sha1=
max-world-size=29999984
server-port=25565
server-ip=
spawn-npcs=true
allow-flight=false
level-name=world
view-distance=10
resource-pack=
spawn-animals=true
white-list=false
generate-structures=true
online-mode=false
max-build-height=256
level-seed=
prevent-proxy-connections=false
use-native-transport=true
enable-rcon=false
motd=YC Minecraft Server
```
## Rule
* 箱子資源可自取
* 苦力怕坑要填平(不用填滿)

## 據點

| 編號 | 座標 | 註 |
| -------- | -------- | -------- |
| 1     | 127,77,-43     | 最初重生點附近     |
| 2     | -4737,65,542     | 林地宅邸  |
| 3     | -1477,67,285     |  沙漠遺跡    |
| 4     | -1780,69,394     | 沙漠村莊     |
| 5     | -1482,67,285     | 沙漠遺跡     |
| 6     |    -   |   -    |

