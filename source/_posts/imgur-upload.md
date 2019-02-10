---
title: Imgur upload API 上傳圖片
date: 2019-02-10 11:55:00
tags:
- imgur
- jQuery
thumbnail: ./myimages/image1.jpg
---


# 註冊應用程式

https://api.imgur.com/oauth2/addclient
![](https://i.imgur.com/kNyS0IW.png)


勾選 OAuth 2.0 without callback URL
然後就會有Client ID

# Source code

## HTML
```html
<form id="imgur">
    <input type="file" class="imgur" accept="image/*" data-max-size="5000"/>
</form>
```

## JS
```js 
$("document").ready(function () {
    $('input[type=file]').on("change", function () {
        var $files = $(this).get(0).files;
        var formData = new FormData();
        formData.append("image", $files[0]);
        if ($files.length) {
            // Reject big files
            if ($files[0].size > $(this).data("max-size") * 1024) {
                console.log("Please select a smaller file");
                return false;
            }
            
            var apiUrl = 'https://api.imgur.com/3/image';
            var apiKey = 'Your Client ID';
            var settings = {
                async: true,
                crossDomain: true,
                url: apiUrl,
                method: "POST",
                datatype: "json",
                headers: {
                    Authorization: "Client-ID " + apiKey
                },
                processData: false,
                contentType: false,
                data: formData,
                beforeSend: function () {
                    console.log("uploading...");
                },
                success: function (res) {
                    $('body').append('<img src="' + res.data.link + '" height = "200px"/>');
                },
                error: function () {
                    alert("upload failed");
                }
            }
            $.ajax(settings).done(function (res) {
                console.log("Done");
            });
        }
    });
});
```
<iframe width="100%" height="300" src="//jsfiddle.net/syokujinau/p07sejcd/embedded/js,html,result/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>



