---
title: Raspberry Pi3 MJPEG-Streamer Install & Setup
date: 2019-02-02 16:20:47
tags:
- Raspberry Pi
- MJPEG-Streamer
categories:
- IoT
thumbnail: https://i.imgur.com/f9eSaX7.jpg
---



# 材料
* Raspberry pi3 / Raspbian
* ktnet KTCCD323 iWatch 5000萬 網路攝影機 (199元)

<img src="https://i.imgur.com/KiCiVae.png" height="189"  >


# Install
```
# 更新&安裝
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get install build-essential libjpeg8-dev imagemagick libv4l-dev cmake -y

# Clone Repo 到 /Downloads
cd /Downloads
git clone https://github.com/jacksonliam/mjpg-streamer.git
cd mjpg-streamer/mjpg-streamer-experimental

# Make
make
sudo make install

# 執行
/usr/local/bin/mjpg_streamer -i "input_uvc.so -r 1280x720 -d /dev/video0 -f 30 -q 80" -o "output_http.so -p 8080 -w /usr/local/share/mjpg-streamer/www"
```
<!-- more -->

# Service
pi3 IP : 192.168.1.8
http-server IP : 192.168.1.161

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>Web Stream</title>
</head>
<body>

<h1>XDD</h1>
<img src="http://192.168.1.8:8080/?action=stream" />

</body>
</html>
```
