---
title: Express.js 接收 Text/Plain 請求只解析出空物件問題
tags:
  - vue
  - express
keywords:
  - vue
  - express
  - text plain
abbrlink: ccb6df18
date: 2021-10-29 16:00:00
---

最近使用 Express 接收 `FilePond` 套件回傳的 `Content-Type: text/plain` 請求時，透過 `req.body` 取得資料時總是拿到空資料。

解法是要另外使用 `express.text()` 來進行解析：

<!--more-->

```javascript
// app.js
app.use(express.text())
```

網路上大部分查到的是用 `body-parser` 的解法，但此方法已 Deprecated。
但這邊有個地方需要特別注意：`express.text()` 需要 `4.17.0` 以上才能夠使用，可透過 `npm i express@latest --save` 升級。

# 參考資料

[Express 4.x API](https://expressjs.com/en/4x/api.html#express.text)
