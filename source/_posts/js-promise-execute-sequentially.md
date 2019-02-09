---
title: JavaScript 筆記 - 依序執行function
date: 2019-02-03 15:07:42
tags:
- JavaScript
- Promise
- ES6
thumbnail: https://i.imgur.com/X3i6f9L.png

---




![](https://i.imgur.com/atfAxVR.png)
之前做C3.js圖表平移效果，網頁載入後平移9次，於是寫了9次callback

```js
setTimeout(function(){console.log('shift ', 1)}, 1000);
setTimeout(function(){console.log('shift ', 2)}, 2000);
setTimeout(function(){console.log('shift ', 3)}, 3000);
setTimeout(function(){console.log('shift ', 4)}, 4000);
setTimeout(function(){console.log('shift ', 5)}, 5000);
setTimeout(function(){console.log('shift ', 6)}, 6000);
setTimeout(function(){console.log('shift ', 7)}, 7000);
setTimeout(function(){console.log('shift ', 8)}, 8000);
setTimeout(function(){console.log('shift ', 9)}, 9000);
```

(｀・ω・´)........
<!-- more -->

呵

<br><br><br><br><br><br><br><br><br>

宣告function回傳promise
```js 
const shiftPromise = (round) => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log(`shift ${round}`);
            resolve(round);
        }, 1000);
    });
}
```

### 解法1: resolve()

```js 
Promise.resolve()
    .then(() => shiftPromise(1))
    .then(() => shiftPromise(2))
    .then(() => shiftPromise(3))
    .then(() => shiftPromise(4))
    .then(() => shiftPromise(5))
    .then(() => shiftPromise(5))
    .then(() => shiftPromise(6))
    .then(() => shiftPromise(7))
    .then(() => shiftPromise(8))
    .then(() => shiftPromise(9))
```
將return的promise一直串下去，但這方法還是很醜，要寫9次

### 解法2: reduce()

展開跟解法1一樣
> arr.reduce(callback[accumlator, currentValue, currentIndex, array], initialValue)

```js
[1, 2, 3, 4, 5, 6, 7, 8, 9].reduce((p, current) => {
    return p.then(() => shiftPromise(current))
},Promise.resolve())  

// 簡寫 去掉return、curly braces
[1, 2, 3, 4, 5, 6, 7, 8, 9].reduce(
    (p, current) => p.then(() => shiftPromise(current)),
    Promise.resolve()
)  
```


### 解法3: async/await

這是最簡潔的寫法! 這種情況可以用IIFE去執行

```js
(async(dataArray) => {
    for(let i = 0; i < dataArray.length; i++) {
        await shiftPromise(dataArray[i]);
    }
})([1, 2, 3, 4, 5, 6, 7, 8, 9]);
```



