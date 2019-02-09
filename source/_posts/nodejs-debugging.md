---
title: Node.js Debugging 筆記
date: 2018-05-11 17:36:33
tags: 
- Node.js
---



## Command Line

> node版本一定要是8以上!!

### 進入debug mode
```
$node inspect app.js
```

操作:

* 單步執行 :
```
debug> n  #next
```
* 進入REPL: 
```
debug> repl #進入REPL後可以輸入變數名稱查看其值，或執行任何js指令
            #用Ctrl+C回到debug mode
```
* 全部執行
```
debug> c #continue
         #Ctrl+C離開debug mode
```
### 常用技巧: 程式插入中斷點
<!-- more -->
>可以配合[nodemon](https://www.npmjs.com/package/nodemon)使用

```js=
//app.js
var person = {
    name : 'yc'
};

person.age = 25;

debugger; //中斷點

person.name = 'Mike';

console.log(person);
```

執行inspect

```
$node inspect app.js
```
**然後continue**

```
debug> c
```

如此一來會直接跳到，中斷點

![](https://i.imgur.com/7EuGPO0.png)




---

![](https://i.imgur.com/DoSmKk3.png)
## Chrome DevTools 
### 1. 進入debug mode
```
$node --inspect-brk app.js #也可用nodemon
```

### 2. 開啟Chrome
```
URL: chrome://inspect
```

![](https://i.imgur.com/CFbCyXA.png)

![](https://i.imgur.com/CjcKNox.png)


