---
title: Fackbook Bot Webhook架設筆記
date: 2018-10-22 21:37:49
tags:
- Facebook
- Ngrok
- base64
- Firebase
thumbnail: 
---




> 當時參加2018 NASA Hackathon做的Facebook聊天機器人，雖然沒得名，但是學會了FB Bot製作、後端database串接，base64 等等
> 還算是有收穫 呵呵

* 官方 [快速入門教學課程](https://developers.facebook.com/docs/messenger-platform/getting-started/webhook-setup)
* [FB Developers](https://developers.facebook.com/apps/)

基本上照著做即可

## Note

1. 過程中要記得安裝request套件
2. 在[開發者頁面](https://developers.facebook.com/apps/)要把VERIFY_TOKEN填入(自己記得就好)，對應到的code的VERIFY_TOKEN

![](https://i.imgur.com/ob4RLqy.png)
<!-- more -->
![](https://i.imgur.com/3Hajp91.png)
3. 在 Messenger 設定主控台的「權杖產生」區段，點擊「選擇粉絲專頁」下拉式功能表，然後選擇您要這個應用程式訂閱的 Facebook 粉絲專頁。這個粉絲專頁就是當用戶在 Messenger 與其交談時，想要 Webhook 接收事件的粉絲專頁
4. 要訂閱這個粉專!!!
5. 要使這個應用程式上線，需要有隱私權政策網址，可以用[產生器](https://privacypolicies.com/)產生
6. 要提交pages_messaging功能才能讓開發者以外的人使用訊息
7. 提交審查之前，要先上傳**1024x1024 pixel的透明背景圖**，以及勾選**商業用途**
> Ref:http://animabeautifullife.blogspot.com/2016/06/facebook-messenger-api.html

## Ngrok

省去webhook在app engine或heroku等等平台部屬的時間，等debug完成再部屬(記得更改webhook網址)，加速開發!
> Reference: https://blog.techbridge.cc/2018/05/24/ngrok/

## Code

```js=
'use strict';

const myToken = 'YOUR TOKEN';


// Imports dependencies and set up http server
const
  express = require('express'),
  bodyParser = require('body-parser'),
  app = express().use(bodyParser.json()); // creates express http server

const request = require('request');
const image2base64 = require('image-to-base64'); //upload base64 encode image on firebase


// Sets server port and logs message on success
app.listen(process.env.PORT || 3000, () => console.log('webhook is listening'));

app.get('/', (req, res) => {
    res.send(`hello yc!!!`);
})

//1
// Creates the endpoint for our webhook 
app.post('/webhook', (req, res) => {  
 
    // Parse the request body from the POST
    let body = req.body;
  
    // Checks this is an event from a page subscription
    if (body.object === 'page') {
  
      // Iterates over each entry - there may be multiple if batched
      body.entry.forEach(function(entry) {
  
        // Gets the message. entry.messaging is an array, but 
        // will only ever contain one message, so we get index 0
        let webhook_event = entry.messaging[0];
        console.log(webhook_event);

        // Get the sender PSID
        let sender_psid = webhook_event.sender.id;
        console.log('Sender PSID: ' + sender_psid);

        // Check if the event is a message or postback and
        // pass the event to the appropriate handler function
        if (webhook_event.message) {
            // handleMessage(sender_psid, webhook_event.message);
            
            switch(webhook_event.message.text) {
              case 'hi':
                  webhook_event.message.text = 'Hi :))';
                  handleMessage(sender_psid, webhook_event.message);
                  break;
              default:
                  //直接回覆原字串 或傳入attachment
                  handleMessage(sender_psid, webhook_event.message);
            }
            
        } else if (webhook_event.postback) {
            handlePostback(sender_psid, webhook_event.postback);
        }        

      });
  
      // Returns a '200 OK' response to all requests
      res.status(200).send('EVENT_RECEIVED');
    } else {
      // Returns a '404 Not Found' if event is not from a page subscription
      res.sendStatus(404);
    }
  
});

//2

// Adds support for GET requests to our webhook
app.get('/webhook', (req, res) => {
    console.log('get webhook');
    // Your verify token. Should be a random string.
    let VERIFY_TOKEN = "12345" //對應到FB developer頁面的驗證權杖
      
    // Parse the query params
    let mode = req.query['hub.mode'];
    let token = req.query['hub.verify_token'];
    let challenge = req.query['hub.challenge'];
      
    // Checks if a token and mode is in the query string of the request
    if (mode && token) {
    
      // Checks the mode and token sent is correct
      if (mode === 'subscribe' && token === VERIFY_TOKEN) {
        
        // Responds with the challenge token from the request
        console.log('WEBHOOK_VERIFIED');
        res.status(200).send(challenge);
      
      } else {
        // Responds with '403 Forbidden' if verify tokens do not match
        res.sendStatus(403);      
      }
    }
});


// Handles messages events
function handleMessage(sender_psid, received_message) {
  let response;
  
  // Checks if the message contains text
  if (received_message.text) {    
    // Create the payload for a basic text message, which
    // will be added to the body of our request to the Send API
    response = { 
      //"text": `You sent the message: "${received_message.text}". Now send me an attachment! \nfrom handleMessage`
      "attachment": {
        "type": "template",
        "payload": {
            "template_type": "generic",
            "elements": [{
                "title": "Title test",
                "subtitle": `subtitle`,
                "image_url": '',
                "buttons": [
                    {
                      "type": "web_url",
                      "url": 'www.google.com',
                      "title":"Google"
                    }
                ],
            }]
        }
      }
    }
  } else if (received_message.attachments) {
    // Get the URL of the message attachment
    let attachment_url = received_message.attachments[0].payload.url;
    // console.log(`[XDDDDDDD] ${JSON.stringify(received_message.attachments, undefined, 4)}`);
    response = {
        "attachment": {
          "type": "template",
          "payload": {
              "template_type": "generic",
              "elements": [{
                  "title": "回報系統",
                  "subtitle": `25.024782°N 121.528864°E`,
                  "image_url": attachment_url,
                  "buttons": [
                      {
                        "type": "web_url",
                        "url": NASAwebpage,
                        "title":"火災現況"
                      }
                  ],
              }]
          }
        }
    }
    const FirebaseURL = 'https://nasa-hackthon-linebot.firebaseio.com/firespot.json';

    image2base64(attachment_url) // you can also to use url
    .then((response) => {
        let MyData = JSON.stringify({
            img : response,
            location: {
                latitude: 121.528864,
                longitude: 25.024782
            },
            status: "未通報"
        })
        
        request({
            "uri": FirebaseURL,
            "method": "POST",
            "body" : MyData
        }, (err, res, body) => {
            if (!err) {
                //console.log(res);
            } else {
                console.log(err);
            }
        });
        
    })
    .catch((error) => {
        console.log(error); //Exepection error....
    });

    
  } 
  
  // Send the response message
  callSendAPI(sender_psid, response);    
}

// Handles messaging_postbacks events
function handlePostback(sender_psid, received_postback) {
    let response;

    // Checks if the message contains text
    if (received_message.text) {
      
      // Creates the payload for a basic text message, which
      // will be added to the body of our request to the Send API
      response = {
        "text": `You sent the message: "${received_message.text}". Now send me an attachment! \nfrom handlePostback`
      }

    } else if (received_message.attachments) {
    
      // Gets the URL of the message attachment
      let attachment_url = received_message.attachments[0].payload.url;
    
    } 
    
    // Sends the response message
    callSendAPI(sender_psid, response); 
}

function callSendAPI(sender_psid, response) {
    // Construct the message body
    let request_body = {
        "recipient": {
          "id": sender_psid
        },
        "message": response
    }
    
    // Send the HTTP request to the Messenger Platform
    request({
        "uri": "https://graph.facebook.com/v2.6/me/messages",
        "qs": { "access_token": myToken }, //PAGE_ACCESS_TOKEN
        "method": "POST",
        "json": request_body
    }, (err, res, body) => {
    if (!err) {
        console.log('message sent!')
    } else {
        console.error("Unable to send message:" + err);
    }
    }); 
}

// playground
async function getLatestInfo() { // Promise
  let year =  new Date().getFullYear()
  let month = new Date().getMonth() + 1
  let date = new Date().getDate()
  month = (month < 10)? '0'+month : month;
  date = (date < 10)? '0'+date : date;   
  return new Promise((resolve, reject) => {
      request({
        "uri": `https://launchlibrary.net/1.3/launch/${year}-${month}-${date}`,
        "method": "GET",
        "json": true
      }, (err, res, body) => {
        if (!err) {
            // console.log(`[QUERY] ${year}-${month}-${date}`)
            // console.log(body.launches[0])
            let latest = body.launches[0];
            resolve({
                date: latest.windowstart,
                name: latest.name,
                lat:  latest.location.pads[0].latitude,
                lng:  latest.location.pads[0].longitude,
                gmap: latest.location.pads[0].mapURL
            })
            // console.log(data);
        } else {
            reject("Unable to send message:" + err);
        }
      });
  })
}
/*
Usage:

getLatestInfo().then((res) => {
    // handle 
}).catch((err) => {
    // error 
})
*/

var fbIDtoTimestamp = (id) => {
  var PUSH_CHARS = "-0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ_abcdefghijklmnopqrstuvwxyz";
  id = id.substring(0,8);
  var timestamp = 0;
  for (var i=0; i < id.length; i++) {
    var c = id.charAt(i);
    timestamp = timestamp * 64 + PUSH_CHARS.indexOf(c);
  }
  return timestamp;
}
```