---
title: React開發環境設定
date: 2019-02-17 13:23:37
tags:
- React
- ES6
- npm
thumbnail: https://i.imgur.com/5VOk9jX.png
---



## Babel Setup

> Babel是一種ES6編譯器，因為有些瀏覽器未支援新語法，所以需要轉換舊版語法

```
npm init
npm install -g babel-cli@6.24.1
npm install babel-preset-react@6.24.1 babel-preset-env@1.5.2
```

### Compile
```
babel src/app.js --out-file=public/scripts/app.js --presets=env,react
```
更改即時編譯，搭配[live-server](https://www.npmjs.com/package/live-server)使用更方便
```
babel src/app.js --out-file=public/scripts/app.js --presets=env,react --watch
```
<!-- more -->
## Basic 

```
├─public
│  │─index.html
│  └─scripts
│     └─app.js
└─src
   └─app.js
```

```html=
<!DOCTYPE html>
<html>

<head>
  <meta charset="UTF-8">
  <title>Basic</title>
</head>

<body>
  <div id="app"></div>
  <script src="https://unpkg.com/react@16.0.0/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@16.0.0/umd/react-dom.development.js"></script>
  <script src="/scripts/app.js"></script>
</body>

</html>
```

```js=
// src/app.js
const render = () => {
    const jsx = (
        <div>
            <h1>My APP</h1>
        </div>
    );

    ReactDOM.render(jsx, document.getElementById('app'))
};

render(); 
```

