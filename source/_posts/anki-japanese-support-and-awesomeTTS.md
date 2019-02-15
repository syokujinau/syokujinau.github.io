---
title: Anki - 使用AwesomeTTS與Japanese Support製作TTS卡片
date: 2019-02-15 13:34:01
tags:
- Anki
- HTML
- CSS
thumbnail: https://i.imgur.com/wH9EPrj.jpg
---

希望能製作一種卡片，看中文可以想到日文，訓練自己的中翻日能力，並且自動產生振假名，好處看背面的時候google小姐會唸給你聽增強記憶XD。

# Install

請先安裝以下2種套件，才能使用他們的功能tags
* [AwesomeTTS](https://ankiweb.net/shared/info/301952613)
* [Japanese Support](https://ankiweb.net/shared/info/3918629684)


# 基本日文卡片

先來看看由Japanese Support提供的`Japanese (recognition)`的卡片樣板，可以看到它是根據Expression為Reading欄位加上振假名。

正面:
```html 
<div class=jp> {{Expression}}  </div>
```

樣式:
```css
.card {
 font-family: arial;
 font-size: 20px;
 text-align: center;
 color: black;
 background-color: white;
}
.jp { font-size: 30px }
.win .jp { font-family: "MS Mincho", "ＭＳ 明朝"; }
.mac .jp { font-family: "Hiragino Mincho Pro", "ヒラギノ明朝 Pro"; }
.linux .jp { font-family: "Kochi Mincho", "東風明朝"; }
.mobile .jp { font-family: "Hiragino Mincho ProN"; }
```
<!-- more -->
背面:
```html 
{{FrontSide}}

<hr id=answer>

<div class=jp> {{furigana:Reading}} </div><br>
{{Meaning}}
```

# 加上TTS與振假名功能

## Step1
工具→管理筆記類型→新增，選擇下圖筆記類型


<img src="https://i.imgur.com/JrPc7IE.png" width="50%" />

## Step2
隨意命名筆記名稱，我是叫`Japanese - 中翻日TTS`
<img src="https://i.imgur.com/ilHo2wi.png" width="80%" />

改欄位順序(也可以不改順序)，欄位名稱Expression與Reading不能自己命名，因為這是Japanese Support定義的，到時候分別輸入:
1. Meaning: 中文
2. Expression: 日文
3. Reading: 日文含振假名

AwesomeTTS會唸出Expression(不能唸Reading欄位因為振假名也會被唸出來)

<img src="https://i.imgur.com/Dwmm6G1.png" width="80%" />

## Step3
選擇`Japanese - 中翻日TTS`→卡片
修改正背面與樣式
![](https://i.imgur.com/8w18NEB.png)

欄位名稱會用{}包住，類似[mustache](https://mustache.github.io/)樣板字串，然後背面樣板中的Expression用tts tag包住就能發音，此外因為Reading會顯示日文含振假名，所以Expression可以隱藏

### Source code
正面:
```html 
<div class=jp hidden> {{Expression}} </div>
{{Meaning}}
<!-- 
1. Meaning: 中文
2. Expression: 日文
3. Reading: 日文含振假名(由Japanese Support自動生成)
-->
```

樣式:
```css
.card {
 font-family: arial;
 font-size: 20px;
 /* text-align: center;*/ 
 /* 我習慣文字靠左，想要置中就把上面註解拿掉 */
 color: black;
 background-color: white;
}
.jp { font-size: 30px }
.win .jp { font-family: "MS Mincho", "ＭＳ 明朝"; }
.mac .jp { font-family: "Hiragino Mincho Pro", "ヒラギノ明朝 Pro"; }
.linux .jp { font-family: "Kochi Mincho", "東風明朝"; }
.mobile .jp { font-family: "Hiragino Mincho ProN"; }
```

背面:
```html 
{{Meaning}}
<hr id=answer>
<tts service="google" voice="ja" hidden> {{Expression}} </tts> 
<!--  -->
<div class=jp> {{furigana:Reading}} </div><br>
```

# Usage

中翻日

<img src="https://i.imgur.com/Bp3gO80.png" width="80%" />

<br/>

<img src="https://i.imgur.com/608vrKw.png" width="80%" />

<hr/>

當然用法不只有中翻日，也可以在Meaning輸入日文意思或圖片

<img src="https://i.imgur.com/fbYaijc.png" width="80%" />

<hr/>

圖翻日
<img src="https://i.imgur.com/6e3Nx7W.png" width="80%" />